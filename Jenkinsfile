pipeline {
    agent any

    environment {
        KONNECT_CONTROL_PLANE_NAME = 'local-gateway'
    }

    stages {
        stage('Checkout code') {
            steps {
                checkout scm
            }
        }
        stage('setup deck') {
            steps {
                sh '''
                    set -euo pipefail
                    mkdir -p $WORKSPACE/.bin
                    curl -sL https://github.com/kong/deck/releases/download/v1.55.0/deck_1.55.0_linux_amd64.tar.gz -o deck.tar.gz
                    tar -xf deck.tar.gz -C $WORKSPACE/.bin
                    chmod +x $WORKSPACE/.bin/deck
                    $WORKSPACE/.bin/deck version
                '''
            }
        }
        stage('Validate kong state') {
            steps {
                withCredentials([string(credentialsId: 'KONNECT_TOKEN', variable: 'KONNECT_TOKEN')]) {
                    sh '''
                        set -eu
                        $WORKSPACE/.bin/deck gateway validate \
                            --konnect-compatibility \
                            --konnect-control-plane-name $KONNECT_CONTROL_PLANE_NAME \
                            --konnect-token $KONNECT_TOKEN \
                            kong.yaml
                    '''
                }
            }
        }
        stage('Sync gateway settings') {
            steps {
                withCredentials([string(credentialsId: 'KONNECT_TOKEN', variable: 'KONNECT_TOKEN')]) {
                    sh '''
                        set -eu
                        $WORKSPACE/.bin/deck gateway sync \
                            --konnect-control-plane-name $KONNECT_CONTROL_PLANE_NAME \
                            --konnect-token $KONNECT_TOKEN \
                            kong.yaml
                    '''
                }
            }
        }
    }

    post {
        success {
            echo 'Kong configuration synced successfully!'
        }
        failure {
            echo 'Deployment failed. Please check the pipeline execution logs.'
        }
    }
}
