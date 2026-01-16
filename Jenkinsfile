pipeline {
    agent any

    environment {
        KONNECT_CONTROL_PLANE_NAME = 'local-gateway'
        DECK_IMAGE = 'kong/deck:v1.55.0'
    }

    stages {
        stage('Checkout code') {
            steps {
                checkout scm
            }
        }
        stage('Validate kong state') {
            steps {
                withCredentials([string(credentialsId: 'KONNECT_TOKEN', variable: 'KONNECT_TOKEN')]) {
                    script {
                        docker.image(env.DECK_IMAGE).inside {
                            sh 'deck gateway validate --konnect-compatibility --konnect-control-plane-name ${env.KONNECT_CONTROL_PLANE_NAME} --konnect-token ${env.KONNECT_TOKEN} kong.yaml'
                        }
                    }
                }
            }
        }
        stage('Sync gateway settings') {
            steps {
                withCredentials([string(credentialsId: 'KONNECT_TOKEN', variable: 'KONNECT_TOKEN')]) {
                    script {
                        docker.image(env.DECK_IMAGE).inside {
                            sh 'deck gateway sync --konnect-control-plane-name ${env.KONNECT_CONTROL_PLANE_NAME} --konnect-token ${env.KONNECT_TOKEN} kong.yaml'
                        }
                    }
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
