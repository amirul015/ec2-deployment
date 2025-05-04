#!/usr/bin/env groovy
pipeline {
    agent { label 'local' }
    environment {
        APPLICATION_NAME = 'CDH'
        COMPONENT_NAME = 'TestAutomation'
        TARGET_ENVIRONMENT = 'BAUDIT'
    }
    options {
        timeout(time: 1, unit: 'HOURS')
        buildDiscarder(logRotator(numToKeepStr: '5'))
        disableConcurrentBuilds()
    }
    stages {
        stage('Checkout Code') {
            steps {
                checkout scm
            }
        }
        stage('Build Zip') {
            steps {
                script {
                    def zipFileName = "${env.APPLICATION_NAME}.${env.COMPONENT_NAME}.zip"
                    sh """
                        zip -r ${zipFileName} . -x "*.git*" "*.zip"
                    """
                }
            }
        }
        stage('Distribute Zip') {
            steps {
                script {
                    def zipFileName = "${env.APPLICATION_NAME}.${env.COMPONENT_NAME}.zip"
                    def targets = [
                        '/mnt/storage/automation-zips/location1',
                        '/mnt/storage/automation-zips/location2'
                    ]
                    for (target in targets) {
                        sh """
                            mkdir -p ${target}
                            cp -rf ${zipFileName} ${target}/
                        """
                    }
                    echo "Zip file ${zipFileName} copied to all targets successfully."
                }
            }
        }
        stage('Tag') {
            when {
                anyOf {
                    branch 'develop'
                    branch pattern: 'release/.*', comparator: 'REGEXP'
                }
                expression { return (env.BUILD_TYPE == 'CICD') }
            }
            steps {
                script {
                    tagSourceCode()
                }
            }
        }
    }
    post {
        always {
            cleanWs()
        }
    }
}
