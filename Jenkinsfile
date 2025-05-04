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
        stage('Build Zip') {
            steps {
                script {
                    def zipFileName = "${env.APPLICATION_NAME}.${env.COMPONENT_NAME}.zip"
                    sh """
                        zip -r ${zipFileName} . -x "*.git*" -x "*.zip"
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
                            cp -f ${zipFileName} ${target}/
                        """
                    }
                    echo "Zip file copied to all targets successfully."
                }
            }
        }
    }

    post {
        always {
            script {
                echo 'Cleaning workspace...'
                // deleteDir()
            }
        }
    }
}
