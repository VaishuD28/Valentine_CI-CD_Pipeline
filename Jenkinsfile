pipeline {
    agent any

    environment{
        SCANNER_HOME= tool 'sonar-scanner'
    }
    stages {
        stage('Valentien day') {
            steps {
                echo "Happy Valentine's Day"
            }
        }
        stage('Git Checkout') {
            steps {
                git branch: 'main', credentialsId: 'git-cred', url: 'https://github.com/Arivazhagan0697/Valentine_CI-CD_Pipeline.git'
            }
        }
        stage('Trivy Filesystem Scan') {
            steps {
                sh "trivy fs --format table -o trivy-fs-report.html ."
            }
        }
        stage('SonarQube Analsyis') {
            steps {
                withSonarQubeEnv('sonar') {
                    sh ''' $SCANNER_HOME/bin/sonar-scanner -Dsonar.projectName=Valentine -Dsonar.projectKey=Valentine \
                            -Dsonar.java.binaries=. '''
                }
            }
        }
        stage('Build & Tag Docker Image') {
           steps {
               script {
                   withDockerRegistry(credentialsId: 'docker-cred', toolName: 'docker') {
                        sh "docker build -t arivuboi27/valentine:v1 ."
                    }
               }
            }
        }
        stage('Docker Image Scan') {
            steps {
                sh "trivy image --format table -o trivy-image-report.html arivuboi27/valentine:v1 "
            }
        }
        stage('Push Docker Image') {
            steps {
               script {
                   withDockerRegistry(credentialsId: 'docker-cred', toolName: 'docker') {
                        sh "docker push arivuboi27/valentine:v1"
                    }
               }
            }
        }
        stage('Deployment to Container') {
            steps {
               sh "docker run -d -p 8081:80 arivuboi27/valentine:v1"
            }
        }
    }
}
