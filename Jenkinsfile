pipeline {
    agent any

    tools {
        maven 'localMaven'
        jdk 'local_jdk'
    }

    stages {
        stage('Build') {
            steps {
                sh 'mvn clean package'
            }
            post {
                success {
                    echo 'Now archiving ...'
                    archiveArtifacts artifacts: '**/target/*.war'
                    echo '... done!'
                }
            }
        }

        stage('Deploy to staging') {
            steps {
                build job: 'Deploy-to-staging'
            }
        }

        stage('Deploy to production') {
            steps {
                timeout( time: 5, unit: 'DAYS' ) {
                    input message: 'Approve deployment to production?'
                }

                build job: 'deploy-to-prod'
            }

            post {
                success {
                    echo '(-: Deployed to production. :-)'
                }

                failure {
                    echo '*** Failed to deploy to production. ***'
                }
            }
        }
    }
}
