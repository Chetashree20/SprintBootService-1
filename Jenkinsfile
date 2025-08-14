pipeline {
    agent any

    stages {
        stage('Build JAR') {
            steps {
                script {
                    try {
                        sh 'mvn clean package -DskipTests'
                        sh 'mkdir -p /mnt/jars'
                        sh 'cp target/*.jar /mnt/jars/app.jar'
                        echo '✅ JAR build SUCCESS'
                    } catch (err) {
                        echo '❌ JAR build FAILED'
                        error("Stopping pipeline due to build failure")
                    }
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    try {
                        sh 'docker build -t chetu20/springboot:1.0 .'
                        echo '✅ Docker image build SUCCESS'
                    } catch (err) {
                        echo '❌ Docker image build FAILED'
                        error("Stopping pipeline due to docker build failure")
                    }
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                script {
                    try {
                        sh 'echo "my-dockerhub-password" | docker login -u "chetu20" -p "Chetu20"'
                        sh 'docker push chetu20/springboot:1.0'
                        echo '✅ Docker push SUCCESS'
                    } catch (err) {
                        echo '❌ Docker push FAILED'
                        error("Stopping pipeline due to push failure")
                    }
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                script {
                    try {
                        sh 'kubectl apply -f deployment.yaml'
                        sh 'kubectl apply -f service.yaml'
                        echo '✅ Kubernetes deploy SUCCESS'
                    } catch (err) {
                        echo '❌ Kubernetes deploy FAILED'
                        error("Stopping pipeline due to deploy failure")
                    }
                }
            }
        }
    }

    post {
        success {
            echo '🎉 Pipeline completed successfully!'
        }
        failure {
            echo '💥 Pipeline failed!'
        }
    }
}
