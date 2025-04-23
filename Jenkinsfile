pipeline {
    agent any
    tools {
        jdk 'OracleJDK17'
        maven 'maven3'
    }
    environment {
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

        stage('Build & Push Image') {
            steps {
                script {
                    docker.withRegistry('https://registry.hub.docker.com', registryCredential) {
                        docker.build("${DOCKER_REGISTRY}/${APP_NAME}:${env.BUILD_ID}")
                              .push()
                    }
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                script {
                    sh """
                        kubectl set image deployment/sbapp \
                        sbapp=${DOCKER_REGISTRY}/${APP_NAME}:${env.BUILD_ID} \
                        --record -n ${KUBE_NAMESPACE}
                    """
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
            cleanWs()
        }
    }
}
