pipeline {
    agent { label 'Jenkins-Agent' }
    
    environment {
        KUBECONFIG_CREDENTIALS_ID = 'kubeconfig-id' // Jenkins credentials ID for kubeconfig
    }

    stages {
        stage('Clone Repo') {
            steps {
                git 'https://github.com/veereshvishu/devops-project1.git'
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
