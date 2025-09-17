# docker hub push and pull on ec2 

```bash
pipeline {
    agent any
    environment {
        NVM_DIR = "${HOME}/.nvm"
        PATH = "${NVM_DIR}/versions/node/v18.16.0/bin:${PATH}"
        DOCKER_IMAGE_TAG = "${BUILD_NUMBER}"
        DOCKER_HUB_REPOSITORY = "sheetalshevalkar/test-project"
    }
    stages {
        stage('Checkout') {
            steps {
                checkout scmGit(branches: [[name: '*/develop']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/Sheetalsgs/myapp.git']])
            }
        }
        stage('Build and Package') {
            steps {
                sh '''
                    . $HOME/.nvm/nvm.sh
                    nvm use v18.16.0
                    node -v
                    npm install
                    npm run build
                '''
            }
        }
        stage("Docker Build") {
            steps {
                sh '''
                    docker version
                    docker build -t ${DOCKER_HUB_REPOSITORY}:${DOCKER_IMAGE_TAG} .
                    docker image list
                '''
            }
        }
        stage("Docker Push") {
            steps {
                withCredentials([string(credentialsId: 'DOCKER_HUB_PASSWORD', variable: 'PASSWORD')]) {
                    sh 'docker login -u sheetalshevalkar -p $PASSWORD'
                    sh 'docker push ${DOCKER_HUB_REPOSITORY}:${DOCKER_IMAGE_TAG}'
                }
            }
        }
        
        stage('Cleaning up') {
            steps {
                sh "docker rmi ${DOCKER_HUB_REPOSITORY}:${DOCKER_IMAGE_TAG}"
            }
        }
        stage("Deployment") {
          steps {
              sshagent(['3691838d-6ec8-45a0-bbc9-c3cd6e287590']) {
                    // Run SSH commands within this block
                   sh 'ssh -t -o StrictHostKeyChecking=no ubuntu@3.110.159.62 docker ps'
                    sh 'ssh -t -o StrictHostKeyChecking=no ubuntu@3.110.159.62 docker stop connectionapp || true'
                    sh 'ssh -t -o StrictHostKeyChecking=no ubuntu@3.110.159.62 docker rm connectionapp || true'
                    sh 'ssh -t -o StrictHostKeyChecking=no ubuntu@3.110.159.62 docker rmi $(docker images -q ${DOCKER_HUB_REPOSITORY} | grep -v ${DOCKER_IMAGE_TAG}) || true'
                    sh 'ssh -t -o StrictHostKeyChecking=no ubuntu@3.110.159.62 docker pull ${DOCKER_HUB_REPOSITORY}:${DOCKER_IMAGE_TAG}'
                    sh 'ssh -t -o StrictHostKeyChecking=no ubuntu@3.110.159.62 docker run -d -p 3000:80 --name connectionapp ${DOCKER_HUB_REPOSITORY}:${DOCKER_IMAGE_TAG}'
                
            }
        }
    }
}

    
}
```