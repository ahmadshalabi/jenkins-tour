pipeline {
    agent any
    options {
        skipStagesAfterUnstable()
    }
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
        stage('Qodana') {
            stages {
                stage('Preparations') {
                    steps {
                        sh "mkdir -p ${env.JENKINS_HOME}/qodana/"
                        sh "chown jenkins:jenkins ${env.JENKINS_HOME}/qodana"
                    }
                }
                stage('Run') {
                    agent {
                        docker {
                            image 'jetbrains/qodana-jvm'
                            args "-v ${env.JENKINS_HOME}/qodana/reports:/data/reports -v ${env.JENKINS_HOME}/qodana/cache:/data/cache -v ${env.JENKINS_HOME}/qodana/results:/data/results -v ${env.JENKINS_HOME}/qodana/qodana.sarif.json:/data/qodana.sarif.json --entrypoint=''"
                        }
                    }
                    steps {
                        sh "qodana --save-report"
                    }
                }
            }
        }
        stage('Deploy - Staging') {
            steps {
                timeout(time: 3, unit: 'MINUTES') {
                    retry(5) {
                        echo './deploy staging'
                        sh './gradlew run'
                        echo './run-smoke-tests'
                    }
                }

                timeout(time: 3, unit: 'MINUTES') {
                    echo 'execute health-check.sh'
                }
            }
        }
        stage('Sanity check') {
            steps {
                input "Does the staging environment look ok?"
            }
        }
        stage('Deploy - Production') {
            steps {
                timeout(time: 3, unit: 'MINUTES') {
                    retry(5) {
                        echo './deploy production'
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
//             deleteDir()
        }
        success {
            echo 'Completed Successfully'
            slackSend channel: '#ops-room',
                        color: 'good',
                        message: "The pipeline ${currentBuild.fullDisplayName} completed successfully."
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