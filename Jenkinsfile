@Library('jenkins-pipeline-shared-libraries')_
agentLabel = "${ADDITIONAL_LABEL : ADDITIONAL_LABEL + ' && ' : ''} kie-rhel7 && !master"

pipeline {
    agent {
        label agentLabel
    }
    tools {
        maven 'kie-maven-3.5.4'
        jdk 'kie-jdk1.8'
    }
    options {
        buildDiscarder logRotator(artifactDaysToKeepStr: '', artifactNumToKeepStr: '', daysToKeepStr: '', numToKeepStr: '10')
        timeout(time: 600, unit: 'MINUTES')
    }
    stages {
        stage('Initialize') {
            steps {
                sh 'printenv'

            }
        }
        stage('Build upstream projects') {
            steps {
                script {
                    def file =  (JOB_NAME =~ /\/[a-z,A-Z\-]*\.downstream\.production/).find() ? 'downstream.production.stages' :
                                (JOB_NAME =~ /\/[a-z,A-Z\-]*\.downstream/).find() ? 'downstream.stages' :
                                'upstream.stages'
                    println "Loading ${file} file..."
                    load("$WORKSPACE/${file}")
                }
            }
        }
    }
    post {
        unstable {
            script {
                mailer.sendEmailFailure()
            }
        }
        failure {
            script {
                mailer.sendEmailFailure()
            }
        }
        always {
            //junit '**/target/surefire-reports/**/*.xml'
            cleanWs()
        }
    }
}
