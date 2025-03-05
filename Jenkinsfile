pipeline {
    agent any

    environment {
        SONARQUBE_SERVER = 'SonarQube'
        SONARQUBE_SCANNER = 'SonarQube Scanner'
    }

    stages {
        stage('Checkout') {
            steps {
                git 'https://github.com/your-repo/your-project.git'
            }
        }

        stage('Build') {
            steps {
                sh './gradlew build'
            }
        }

        stage('Code Quality Analysis') {
            steps {
                withSonarQubeEnv('SonarQube') {
                    sh './gradlew sonarqube'
                }
            }
        }

        stage('Code Coverage') {
            steps {
                jacoco execPattern: '**/build/jacoco/test.exec'
            }
        }

        stage('Cyclomatic Complexity') {
            steps {
                sh 'lizard .'
            }
        }

        stage('Security Vulnerability Scan') {
            steps {
                sh 'dependency-check.sh --project your-project --scan .'
            }
        }

        stage('Quality Gate') {
            steps {
                timeout(time: 1, unit: 'HOURS') {
                    waitForQualityGate abortPipeline: true
                }
            }
        }
    }

    post {
        always {
            junit '**/build/test-results/test/*.xml'
            jacoco execPattern: '**/build/jacoco/test.exec'
            archiveArtifacts artifacts: '**/build/libs/*.jar', allowEmptyArchive: true
        }

        success {
            mail to: 'your-email@example.com',
                 subject: "SUCCESS: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]'",
                 body: "Good news, the build succeeded! Check the details at ${env.BUILD_URL}"
        }

        failure {
            mail to: 'your-email@example.com',
                 subject: "FAILURE: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]'",
                 body: "Unfortunately, the build failed. Check the details at ${env.BUILD_URL}"
        }
    }
}
