pipeline {
    agent any
    environment {
        DOCKERHUB_CREDENTIALS=credentials('docker_hub_cred')
    }

    stages {
        stage('git-checkout') {
            steps {
                checkout scmGit(branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/mohammedashiqu/spring-boot-app.git']])
            }
        }
        stage('build-jar') {
            steps {
                sh 'mvn clean install'
            }
        }
        stage('Delete-image') {
            steps{
                sh 'sudo docker rmi ashiqummathoor/my-image:latest'
            }
        }
        stage('Building image') {
            steps{
                script {
                    dockerImage = docker.build ("ashiqummathoor/my-image:${env.BUILD_ID}")
                    dockerImage = docker.build ("ashiqummathoor/my-image:latest")
                }
            }
        }
        stage('login-to-dokcer-hub') {
            steps {
                sh 'sudo echo $DOCKERHUB_CREDENTIALS_PSW | sudo docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin'
            }
        }
        stage('push') {
            steps {
                sh 'sudo docker push ashiqummathoor/my-image:$BUILD_NUMBER'
                sh 'sudo docker push ashiqummathoor/my-image:latest'
            }
        }
        stage('apply-on-kubernetes-cluster') {
            steps {
                sh 'sudo kubectl replace --force -f kubernetes-app-ashiq.yaml'
            }
        }
    }
}
