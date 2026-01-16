pipeline {
    agent {
        docker {
            image 'kong/deck:v1.55.0'
            reuseNode true
        }
    }

    environment {
        KONNECT_CONTROL_PLANE_NAME = 'local-gateway'
        KONNECT_TOKEN = credentials('KONNECT_TOKEN')
    }

    stages {
        stage('Checkout code') {
            steps {
                checkout scm
            }
        }
        stage('Validate kong state') {
            steps {
                sh 'deck gateway validate --konnect-compatibility --konnect-control-plane-name ${KONNECT_CONTROL_PLANE_NAME} --konnect-token ${KONNECT_TOKEN} kong.yaml'
            }
        }
        stage('Sync gateway settings') {
            steps {
                sh 'deck gateway sync --konnect-control-plane-name ${KONNECT_CONTROL_PLANE_NAME} --konnect-token ${KONNECT_TOKEN} kong.yaml'
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
