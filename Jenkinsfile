pipeline {
    agent any

    tools {
        maven 'localMaven'
        jdk 'local_jdk'
    }

    parameters {
        string ( name: 'tomcat_dev', defaultValue: 'udemy_staging_tomcat', description: 'Development Tomcat')
        string ( name: 'tomcat_prod', defaultValue: 'udemy_production_tomcat', description: 'Production Tomcat')
    }

    triggers {
        pollSCM('* * * * *')
    }

    stages{
        stage('Build'){
            steps {
                sh 'mvn clean package'
            }
            post {
                success {
                    echo 'Now Archiving...'
                    archiveArtifacts artifacts: '**/target/*.war'
                }
            }
        }

        stage('Deployments') {
            parallel {
                stage('Deploy to dev') {
                    steps {
                        sh "docker cp **/target/*.war ${params.tomcat_dev}:/usr/local/tomcat/webapps"
                    }
                }

                stage('Deploy to production') {
                    steps {
                        sh "docker cp **/target/*.war ${params.tomcat_prod}:/usr/local/tomcat/webapps"
                    }
                }
            }   
        }
    }
}