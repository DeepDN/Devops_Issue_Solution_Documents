# Git Action Workflow File to Select Tag Deployment

```yaml
name: workingfile with job seperation

on:
  workflow_dispatch:
    inputs:
      tag:
        description: 'Select a tag to deploy'
        required: true

jobs:
  checkout_and_build:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout code
        uses: actions/checkout@v2
        with:
          ref: ${{ github.event.inputs.tag }}
      - name: Install dependencies
        run: npm install
      - name: Build app
        run: npm run build
      - name: Prepare files
        run: |
          mkdir project
          cp -r dist project/
          cp package.json project/
          cp Dockerfile project/

  configure_aws_credentials:
    runs-on: ubuntu-latest
    needs: checkout_and_build  # Depends on the completion of checkout_and_build
    steps:
      - name: Configure AWS credentials
        run: |
          docker -v
          cat /etc/os-release
          aws configure set aws_access_key_id ${{ secrets.AWS_ACCESS_KEY_ID }}
          aws configure set aws_secret_access_key ${{ secrets.AWS_SECRET_ACCESS_KEY }}
          aws configure set region ap-southeast-1
          aws s3 ls

  login_to_ecr:
    runs-on: ubuntu-latest
    needs: configure_aws_credentials  # Depends on the completion of configure_aws_credentials
    steps:
      - name: Log in to ECR
        run: |
          aws ecr get-login-password --region ap-southeast-1 | docker login --username AWS --password-stdin 589943763826.dkr.ecr.ap-southeast-1.amazonaws.com

  build_and_push_docker_image:
    runs-on: ubuntu-latest
    needs: login_to_ecr  # Depends on the completion of login_to_ecr
    steps:
      - name: Build and tag Docker image
        run: |
          docker build -t prodagentservice .
          docker tag prodagentservice:latest 589943763826.dkr.ecr.ap-southeast-1.amazonaws.com/prodagentservice:agentlatest
          echo 'docker images'
          docker images
      - name: Push Docker image to AWS ECR
        run: |
          docker push 589943763826.dkr.ecr.ap-southeast-1.amazonaws.com/prodagentservice:agentlatest

  deploy_to_ec2:
    runs-on: ubuntu-latest
    needs: build_and_push_docker_image  # Depends on the completion of build_and_push_docker_image
    steps:
      - name: SSH into EC2 instance and pull Docker image
        uses: appleboy/ssh-action@master
        with:
          host: ${{ secrets.PROD_EC2_INSTANCE_IP }}
          username: ${{ secrets.PROD_SSH_USERNAME }}
          key: ${{ secrets.PROD_SSH_PRIVATE_KEY }}
          passphrase: ${{ secrets.EC2_INSTANCE_PASSPHRASE }}
          script: |
            cd /home/ec2-user/workspace/agent-service
            sudo docker stop agentlatest || true
            sudo docker rm -f agentlatest || true
            sudo docker rmi -f 589943763826.dkr.ecr.ap-southeast-1.amazonaws.com/prodagentservice:agentlatest
            docker login -u AWS -p "$(aws ecr get-login-password --region ap-southeast-1)" 589943763826.dkr.ecr.ap-southeast-1.amazonaws.com
            docker pull 589943763826.dkr.ecr.ap-southeast-1.amazonaws.com/prodagentservice:agentlatest
            sudo docker run -d -p 3002:3002 --name agentlatest --env-file .env 589943763826.dkr.ecr.ap-southeast-1.amazonaws.com/prodagentservice:agentlatest
            sudo docker images -f "dangling=true" -q | xargs -r sudo docker rmi
```
