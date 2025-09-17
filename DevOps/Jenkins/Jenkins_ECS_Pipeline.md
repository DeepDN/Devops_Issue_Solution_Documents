# Jenkins ECS Pipeline

```jsx
pipeline {
    agent any
    environment {
        ECR_IMAGE_TAG = "AGENT-PROVISIONING_v_${BUILD_NUMBER}"
        ECR_REPOSITORY = "prod-credebl2.0"
        AWS_REGION = "ap-south-1"
        CLUSTER = "CREDEBL_2-0-BACKEND-PROD"
    }
    stages {
        stage('Comment') {
            steps {
                script {
                    WHAT = input(message: 'Please enter a Description for this deployment:', parameters: [string(defaultValue: '', description: 'WHAT ?', name: 'Comment')])
                    echo "Deployment Comment WHAT?: ${WHAT}"
                    WHY = input(message: 'Please enter a Description for this deployment:', parameters: [string(defaultValue: '', description: 'WHY ?', name: 'Comment')])
                    echo "Deployment Comment WHY?: ${WHY}"
                }
            }
        }
        stage('Checkout') {
            steps {
                checkout([$class: 'GitSCM', branches: [[name: '*/main']], userRemoteConfigs: [[credentialsId: 'jenkins-platform-integration', url: 'https://github.com/credebl/platform.git']]])
                dir('devops') {
                    checkout([$class: 'GitSCM', branches: [[name: '*/main']], userRemoteConfigs: [[credentialsId: 'jenkins-platform-integration', url: 'https://github.com/credebl/cloud-infra.git']]])
                }
            }
        }
        stage('write .env file in workspace') {
            steps {
                writeFile file: '.env', text: '''
DATABASE_URL=postgres://postgres.oqgpktripebxglwnyeex:ceKgBZZmTWWex8Vf@aws-0-ap-south-1.pooler.supabase.com:5432/postgres
POOL_DATABASE_URL=postgres://postgres.oqgpktripebxglwnyeex:ceKgBZZmTWWex8Vf@aws-0-ap-south-1.pooler.supabase.com:6543/postgres?pgbouncer=true
'''
            }
        }
        stage("login to ecr to access base image"){
            steps{
                sh '''aws ecr get-login-password --region ap-south-1 | docker login --username AWS --password-stdin 668004263903.dkr.ecr.ap-south-1.amazonaws.com'''
            }
        }
        
        stage("Docker Build") {
            steps {
                sh '''
                    ls 
                    docker version
                    docker build -t ${ECR_REPOSITORY}:${ECR_IMAGE_TAG} -f devops/prod-taskdef/Dockerfile.agent-provisioning .
                    docker image list
                '''
            }
        }
        stage("Docker Push to ECR") {
            steps {
                withCredentials([
                    string(credentialsId: 'AWS_ACCESS_KEY_ID', variable: 'AWS_ACCESS_KEY_ID'),
                    string(credentialsId: 'AWS_SECRET_ACCESS_KEY', variable: 'AWS_SECRET_ACCESS_KEY'),
                    string(credentialsId: 'AWS_ACCOUNT_ID', variable: 'AWS_ACCOUNT_ID'),
                ]) {
                    script {
                        def ecrLogin = sh(
                            returnStdout: true,
                            script: "aws ecr get-login-password --region ${AWS_REGION} | docker login --username AWS --password-stdin ${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_REGION}.amazonaws.com"
                        )
                        sh "docker tag ${ECR_REPOSITORY}:${ECR_IMAGE_TAG} ${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_REGION}.amazonaws.com/${ECR_REPOSITORY}:${ECR_IMAGE_TAG}"
                        sh "docker push ${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_REGION}.amazonaws.com/${ECR_REPOSITORY}:${ECR_IMAGE_TAG}"
                    }
                }
            }
        }
        stage("Prepare Environment") {
            steps {
                sh """
                    set -x
                    env
                    aws configure list
                    echo \$HOME
                """
            }
        }
        stage("Retrieve Repository URI") {
            steps {
                script {
                    REPOSITORY_URI = sh(
                        returnStdout: true,
                        script: "aws ecr describe-repositories --repository-names ${ECR_REPOSITORY} --region ${AWS_REGION} | jq -r '.repositories[].repositoryUri'"
                    ).trim()
                }
            }
        }
        stage("Update Task Definition") {
            steps {
                dir('devops/prod-taskdef') {
                    script {
                        FAMILY = sh(
                            returnStdout: true,
                            script: "sed -n 's/.*\"family\": \"\\(.*\\)\",/\\1/p' prod-agent-provisioning-taskdef.json"
                        ).trim()
                        NAME = sh(
                            returnStdout: true,
                            script: "jq -r '.containerDefinitions[0].name' prod-agent-provisioning-taskdef.json"
                        ).trim()
                        SERVICE_NAME = "${NAME}"
                        sh """
                            sed -e "s;%BUILD_NUMBER%;${BUILD_NUMBER};g" -e "s;%REPOSITORY_URI%;${REPOSITORY_URI};g" prod-agent-provisioning-taskdef.json > ${WORKSPACE}/${NAME}-v_${BUILD_NUMBER}.json
                            aws ecs register-task-definition --family ${FAMILY} --cli-input-json file://${WORKSPACE}/${NAME}-v_${BUILD_NUMBER}.json --region ${AWS_REGION}
                        """
                    }
                }
            }
        }
      stage("Create or Update Service") {
            steps {
                dir('devops/taskdef') {
                    script {
                        SERVICES = sh(
                            returnStdout: true,
                            script: "aws ecs describe-services --services ${SERVICE_NAME} --cluster ${CLUSTER} --region ${AWS_REGION} | jq -r '.failures[]'"
                        ).trim()
                        
                        REVISION = sh(
                            returnStdout: true,
                            script: "aws ecs describe-task-definition --task-definition ${FAMILY} --region ${AWS_REGION} | jq -r '.taskDefinition.revision'"
                        ).trim()
                        
                        if (SERVICES == "") {
                            echo "Entered existing service"
                            DESIRED_COUNT = sh(
                                returnStdout: true,
                                script: "aws ecs describe-services --services ${SERVICE_NAME} --cluster ${CLUSTER} --region ${AWS_REGION} | jq -r '.services[].desiredCount'"
                            ).trim()
                            
                            if (DESIRED_COUNT == "0") {
                                DESIRED_COUNT = "1"
                            }
                            
                            sh "aws ecs update-service --cluster ${CLUSTER} --region ${AWS_REGION} --service ${SERVICE_NAME} --task-definition ${FAMILY}:${REVISION} --desired-count ${DESIRED_COUNT}"
                        } 
                            
                        
                    }        
                }
            }   
        }
    }
    post {
    always {
        script {
            def currentTime = new Date().format("dd-MM-yyyy HH:mm:ss")
                def buildStatus = currentBuild.result ?: 'UNKNOWN'
                def startedBy = currentBuild.getBuildCauses()[0].shortDescription + " / " + currentBuild.getBuildCauses()[0].userId
                def message = "Build ${buildStatus}: ${env.JOB_NAME} #${env.BUILD_NUMBER} \n ${startedBy} on PROD \n Date/Time: ${currentTime} UTC \n WHAT: ${WHAT} \n WHY: ${WHY}"

                // Send notification to Google Chat
                 googlechatnotification url: 'https://chat.googleapis.com/v1/spaces/AAAAgbFT0F8/messages?key=AIzaSyDdI0hCZtE6vySjMm-WEfRq3CPzqKqqsHI&token=8SWtqRPaIUalrhKSJxiK49LreBFqoleCcGIyVUMoVbc', message: message
            def imageId = sh (
                script: "docker images --format '{{.ID}} {{.Repository}}:{{.Tag}}' | grep '668004263903.dkr.ecr.ap-south-1.amazonaws.com/prod-credebl2.0:${ECR_IMAGE_TAG}' | awk '{print \$1}'",
                returnStdout: true
            ).trim()

            if (imageId) {
                sh "docker image rm -f ${imageId}"
            } else {
                echo "No matching images found to remove."
            }
        }
      cleanWs()
    }
    
}

}

```