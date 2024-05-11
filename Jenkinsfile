pipeline {
    agent any
    tools {
        maven 'Maven3'
    }
    stages {
        stage('Build Maven') {
            steps {
                checkout([$class: 'GitSCM', branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/geneve76/documents.git']]])
                sh 'mvn clean install'
            }
        }
        stage('Build Docker image') {
            steps {
                script {
                    sh 'docker build -t geneve76/hello:latest .'
                }
            }
        }
        stage('Push image to Hub') {
            steps {
                script {
                    withCredentials([string(credentialsId: 'dockerhub-pwd', variable: 'DOCKERHUB_PASSWORD')]) {
                        sh "docker login -u geneve76 -p ${env.DOCKERHUB_PASSWORD}"
                        sh 'docker push geneve76/hello:latest'
                    }
                }
            }
        }
        stage('Deploy to k8s') {
            steps {
                script {
                    kubernetesDeploy(configs: 'deploymentservice.yaml', kubeconfigId: 'k8sconfigpwd')
                }
            }
        }
    }
}
