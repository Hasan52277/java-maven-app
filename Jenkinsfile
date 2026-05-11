pipeline {
    agent any

    tools {
        maven "maven3"
    }

    stages {

        stage('Clean workspace') {
            steps {
                cleanWs()
            }
        }

        stage('Git clone') {
            steps {
                git branch: 'main',
                url: 'https://github.com/Hasan52277/java-maven-app.git'
            }
        }

        stage('Build WAR File') {
            steps {
                sh 'mvn clean package'
            }
        }

        stage('Remove Old Container/Image') {
            steps {
                sh '''
                docker stop javamavenapp_continer || true

                docker rm -f javamavenapp_continer || true

                docker rmi javamavenapp || true

                docker rmi mohdhasan/javamavenapp:latest || true
                '''
            }
        }

        stage('Build Docker Image') {
            steps {
                sh '''
                docker build -t javamavenapp .
                docker tag javamavenapp mohdhasan/javamavenapp:latest
                '''
            }
        }

        stage('Push Docker Image') {
            steps {
                script {
                    withDockerRegistry(credentialsId: 'docker', toolName: 'docker') {

                        sh 'docker push mohdhasan/javamavenapp:latest'

                    }
                }
            }
        }

        stage('Run Docker Container') {
            steps {
                sh '''
                docker run -d \
                -p 9000:8080 \
                --name javamavenapp_continer \
                mohdhasan/javamavenapp:latest
                '''
            }
        }
    }
}
