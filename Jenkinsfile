pipeline {
    agent any
    tools {
        jdk 'OracleJDK17'
        maven 'maven3'
    }
    environment {
        DOCKER_IMAGE = 'veereshvishu/medicure'
        KUBECONFIG_CREDENTIALS_ID = 'kubeconfig-id' // Jenkins credentials ID for kubeconfig
        registryCredential = 'dockerhub'            // Docker Hub credential ID in Jenkins
    }

    stages {
        stage('Clone Repo') {
            steps {
                git 'https://github.com/veereshvishu/devops-project1.git'
            }
        }

        stage('Build with Maven') {
            steps {
                sh 'mvn clean package -DskipTests'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t $DOCKER_IMAGE .'
            }
        }

        stage('Push Docker Image') {
            steps {
                script {
                    docker.withRegistry('https://registry.hub.docker.com', registryCredential) {
                        docker.image(DOCKER_IMAGE).push("latest")
                    }
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                withCredentials([file(credentialsId: "${KUBECONFIG_CREDENTIALS_ID}", variable: 'KUBECONFIG')]) {
                    sh '''
                        kubectl apply -f deployment.yaml --kubeconfig=$KUBECONFIG
                    '''
                }
            }
        }
    }

    post {
        success {
            echo 'Deployment successful!'
        }
        failure {
            echo 'Pipeline failed.'
        }
    }
}
