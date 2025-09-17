# pipeline to build image with build no and delete from local

```bash
pipeline {
    agent any
    environment {
        NVM_DIR = "${HOME}/.nvm"
        PATH = "${NVM_DIR}/versions/node/v18.16.0/bin:${PATH}"
        DOCKER_IMAGE_TAG = "${BUILD_NUMBER}"
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
                    docker build -t sheetalshevalkar/test-project:${DOCKER_IMAGE_TAG} .
                    docker image list
                '''
            }
        }
        stage("Docker Push") {
            steps {
                withCredentials([string(credentialsId: 'DOCKER_HUB_PASSWORD', variable: 'PASSWORD')]) {
                    sh 'docker login -u sheetalshevalkar -p $PASSWORD'
                    sh 'docker push sheetalshevalkar/test-project:${DOCKER_IMAGE_TAG}'
                }
            }
        }
       
        stage('Cleaning up') {
            steps {
                sh "docker rmi sheetalshevalkar/test-project:${DOCKER_IMAGE_TAG}"
            }
        }
    }
}
```