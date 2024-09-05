pipeline {
    agent any
    environment {
        IMAGE = 'devopsacr.azurecr.io/cron-job'
        VERSION = "v${env.BUILD_NUMBER}"
    }
    stages {
        stage('Build') {
            steps {
                script {
                    echo "Building Docker image..."
                    docker.build("${IMAGE}:${VERSION}")
                }
            }
        }
        stage('Push') {
            steps {
                script {
                    echo "Pushing Docker image to Azure Container Registry..."
                    docker.withRegistry('https://devopsacr.azurecr.io', 'azure-acr-credentials') {
                        docker.image("${IMAGE}:${VERSION}").push()
                    }
                }
            }
        }
        stage('Deploy') {
            steps {
                script {
                    echo "Deploying Helm chart to Kubernetes..."
                    sh "helm upgrade --install cronjob helm-chart/ --set image.tag=${VERSION}"
                }
            }
        }
    }
    post {
        success {
            echo "Pipeline completed successfully!"
        }
        failure {
            echo "Pipeline failed. Please check the logs."
        }
    }
}
