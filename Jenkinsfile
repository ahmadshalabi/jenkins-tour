pipeline {
    agent any
    stages {
        stage('Classes') {
            steps {
                sh './gradlew classes'
            }
        }
        stage('TestClasses') {
            steps {
                sh './gradlew testClasses'
            }
        }
        stage('JAR') {
            steps {
                sh './gradlew jar'
            }
        }
        stage('Test') {
            steps {
                sh './gradlew test'
            }
        }
        stage('Check') {
            steps {
                sh './gradlew check'
            }
        }
        stage('Assemble') {
            steps {
                sh './gradlew assemble'
            }
        }
        stage('Build') {
            steps {
                sh './gradlew build'
            }
        }
        stage('Deploy') {
            steps {
                timeout(time: 3, unit: 'MINUTES') {
                    retry(5) {
                        sh './gradlew run'
                    }
                }

                timeout(time: 3, unit: 'MINUTES') {
                    echo 'execute health-check.sh'
                }
            }
        }
    }
    post {
        always {
            archiveArtifacts artifacts: 'build/libs/**/*.jar', fingerprint: true
            junit 'build/test-results/**/*.xml'
            deleteDir()
        }
        success {
            echo 'Completed Successfully'
            slackSend channel: '#ops-room',
                        color: 'good',
                        message: 'The pipeline ${currentBuild.fullDisplayName} completed successfully.'
        }
        failure {
            echo 'Failed'
            mail to: 'eng.ahmadshalabi@gmail.com',
                subject: "Failed Pipeline: ${currentBuild.fullDisplayName}",
                body: "Something is wrong with ${env.BUILD_URL}"

        }
        unstable {
            echo 'Unstable'
        }
        changed {
            echo 'state of the Pipeline has changed'
        }
    }
}