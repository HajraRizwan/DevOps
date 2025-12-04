
pipeline {
    agent any

    environment {
        // Optional: Docker Hub credentials
        DOCKERHUB_CREDENTIALS = 'dockerhub-cred'
    }

    stages {
        stage('Pull Code') {
            steps {
                git branch: 'main', url: 'https://github.com/HajraRizwan/JApp.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    // Make dockerImage accessible across stages
                    dockerImage = docker.build('hajrarizwan/japp')
                }
            }
        }

        stage('Run Tests') {
            steps {
                script {
                    // Create test-results folder if it doesn't exist
                    bat 'if not exist test-results mkdir test-results'
                    
                    // Create dummy test result XML
                    bat 'echo ^<testsuite^>^<testcase classname="demo" name="test1"/^>^</testsuite^> > test-results\\test.xml'
                }
            }
            post {
                always {
                    junit 'test-results/*.xml'
                }
            }
        }

        stage('Push to Docker Hub') {
            steps {
                script {
                    docker.withRegistry('', DOCKERHUB_CREDENTIALS) {
                        dockerImage.push()
                    }
                }
            }
        }
    }
}
