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
                    curl -sL https://github.com/kong/deck/releases/download/v1.55.0/deck_1.55.0_linux_amd64.tar.gz -o deck.tar.gz; \
                    tar -xf deck.tar.gz -C /tmp; \
                    sudo cp /tmp/deck /usr/local/bin/; \
                    deck version
                '''
            }
        }
        stage('Validate kong state') {
            steps {
                withCredentials([string(credentialsId: 'KONNECT_TOKEN', variable: 'KONNECT_TOKEN')]) {
                    sh '''
                        deck gateway validate \
                            --konnect-compatibility \
                            --konnect-control-plane-name ${env.KONNECT_CONTROL_PLANE_NAME} \
                            --konnect-token ${env.KONNECT_TOKEN} \
                            kong.yaml
                    '''
                }
            }
        }
        stage('Sync gateway settings') {
            steps {
                withCredentials([string(credentialsId: 'KONNECT_TOKEN', variable: 'KONNECT_TOKEN')]) {
                    sh '''
                        deck gateway sync \
                            --konnect-control-plane-name ${env.KONNECT_CONTROL_PLANE_NAME} \
                            --konnect-token ${env.KONNECT_TOKEN} \
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
