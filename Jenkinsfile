pipeline {
    agent any
    tools {
        jdk 'OracleJDK17'
        maven 'maven3'
    }
    environment {
        DOCKER_IMAGE = 'veereshvishu/medicure'
        DOCKER_REGISTRY = 'veereshvishu'
        registryCredential = 'dockerhub'            // Docker Hub credential ID in Jenkins
        APP_NAME = 'sbapp'
        KUBE_NAMESPACE = 'default'
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
                script {
                    // Update deployment with new image
                    sh """
                        kubectl apply -f deployment.yaml -n ${KUBE_NAMESPACE}
                    """
                    
                    // Verify deployment
                    sh "kubectl rollout status deployment/${APP_NAME} -n ${KUBE_NAMESPACE}"
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
        always {
            // Clean up workspace
            cleanWs()
        }
    }
}
