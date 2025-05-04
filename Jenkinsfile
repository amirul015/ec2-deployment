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
        skipDefaultCheckout()
        disableConcurrentBuilds()
    }
 
    stages {
 
        stage('Build') {
            steps {
                script {
                    def zipFileName = "${env.APPLICATION_NAME}_${env.COMPONENT_NAME}.zip"
                    sh """
                        mkdir -p TestAutomation
                        zip -r TestAutomation/${zipFileName} . -x "*.git*" -x "TestAutomation/*"

                    """
                }
            }
        }
 
        stage('Distribute Zip') {
            steps {
                script {
                    def zipFileName = "${env.APPLICATION_NAME}_${env.COMPONENT_NAME}.zip"
                    def sourcePath = "TestAutomation/${zipFileName}"
 
                    def targets = [
                        '/mnt/storage/automation-zips/location1',
                        '/mnt/storage/automation-zips/location2'
                    ]
 
                    for (target in targets) {
                        sh """
                            mkdir -p ${target}
                            cp -f ${sourcePath} ${target}/${zipFileName}
                        """
                    }
 
                    echo "Zip file copied to all targets successfully."
                }
            }
        }
 
        stage('Tag') {
            when {
                beforeAgent true
                anyOf { branch 'develop'; branch 'release/*' }
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
            script {
                echo 'cleaning workspace!!!'
                // deleteDir()
            }
        }
    }
}
