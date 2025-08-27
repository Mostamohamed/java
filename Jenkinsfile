pipeline {
    agent any

    tools {
        maven 'mvn-3-5-4'
    }

    environment {
        IMAGE_TAG = "v${BUILD_NUMBER}"
        GITHUB_CRED = credentials("github")
    }

    stages {

        stage('Checkout') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/Mostamohamed/java.git',
                    credentialsId: 'github'
            }
        }

        stage('Build & Test') {
            steps {
                sh "mvn clean package -DskipTests"
                sh "mvn test"
            }
        }

        stage('Archive Artifact') {
            steps {
                archiveArtifacts artifacts: 'target/*.jar'
            }
        }

        stage('Docker Build & Push') {
            steps {
                withCredentials([string(credentialsId: 'docker', variable: 'DOCKER_TOKEN')]) {
                    sh '''
                        echo "$DOCKER_TOKEN" | docker login -u mostafamohamed2001 --password-stdin
                        docker build -t mostafamohamed2001/lab2-cicd22:${IMAGE_TAG} .
                        docker push mostafamohamed2001/lab2-cicd22:${IMAGE_TAG}
                    '''
                }
            }
        }
    }

    post {
        always {
            cleanWs()
        }
        success {
            echo " Build and Docker push completed successfully!"
        }
        failure {
            echo " Build or Docker push failed!"
        }
    }
}
