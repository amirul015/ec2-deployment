#!/usr/bin/env groovy
pipeline {
    agent { label 'local' }
    environment {
        APPLICATION_NAME = 'CDH'
        COMPONENT_NAME = 'TestAutomation'
        TARGET_ENVIRONMENT = 'BAUDIT'
        ZIP_FILE = "${APPLICATION_NAME}.${COMPONENT_NAME}.zip"
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
                    sh '''
                        echo "Current directory: $(pwd)"
                        echo "Running as: $(whoami)"
                        zip -r ${ZIP_FILE} . -x "*.git*" "*.zip"
                        ls -l ${ZIP_FILE}
                    '''
                }
            }
        }
        stage('Distribute Zip') {
            steps {
                script {
                    def targets = [
                        '/mnt/storage/automation-zips/location1',
                        '/mnt/storage/automation-zips/location2'
                    ]
                    for (target in targets) {
                        sh """
                            echo "Creating target dir: ${target}"
                            sudo mkdir -p ${target}
                            sudo chown jenkins:jenkins ${target}
                            sudo cp -rf ${ZIP_FILE} ${target}/
                        """
                    }
                    echo "Zip file ${env.ZIP_FILE} copied to all targets successfully."
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
