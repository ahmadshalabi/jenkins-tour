pipeline {
    agent {
        docker {
            image 'gradle:7.5.1-jdk17-alpine'
        }
    }
    stages {
        stage('Classes') {
            steps {
                sh 'gradle classes'
            }
        }
        stage('TestClasses') {
            steps {
                sh 'gradle testClasses'
            }
        }
        stage('JAR') {
            steps {
                sh 'gradle jar'
            }
        }
        stage('Test') {
            steps {
                sh 'gradle test'
            }
        }
        stage('Check') {
            steps {
                sh 'gradle check'
            }
        }
        stage('Assemble') {
            steps {
                sh 'gradle assemble'
            }
        }
        stage('Build') {
            steps {
                sh 'gradle build'
            }
        }
        stage('Deploy') {
            steps {
                timeout(time: 3, unit: 'MINUTES') {
                    retry(5) {
                        sh 'gradle run'
                    }
                }

                timeout(time: 3, unit: 'MINUTES') {
                    sh 'execute health-check.sh'
                }
            }
        }
    }
    post {
        always {
            echo 'Find status below:'
        }
        success {
            echo 'Completed Successfully'
        }
        failure {
            echo 'Failed'
        }
        unstable {
            echo 'Unstable'
        }
        changed {
            echo 'state of the Pipeline has changed'
        }
    }
}