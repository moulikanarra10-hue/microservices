
pipeline {

    agent any

    triggers {
        pollSCM('H/2 * * * *')
    }

    options {
        timestamps()
    }

    stages {

        stage('Checkout') {
            steps {
                echo 'Retrieving source code from GitHub'
                checkout scm
            }
        }

        stage('Build') {
            steps {
                echo 'Checking Python application files'

                sh '''
                    python3 -m py_compile \
                    user-service\
                    product-service\
                    order-service
                '''

                echo 'Build completed successfully'
            }
        }

        stage('Deploy') {
            steps {
                echo 'Deploying microservices to AWS EC2'

                sh '''
                    set -eu

                    cp user-service \
                    /opt/user-service

                    cp product-service \
                    /opt/product-service

                    cp order-service \
                    /opt/order-service
                '''
            }
        }

        stage('Restart Services') {
            steps {
                echo 'Restarting all microservices'

                sh '''
                    sudo /usr/bin/systemctl restart user-service
                    sudo /usr/bin/systemctl restart product-service
                    sudo /usr/bin/systemctl restart order-service
                '''

                echo 'All services restarted'
            }
        }

        stage('Verify') {
            steps {
                sh '''
                    systemctl is-active user-service
                    systemctl is-active product-service
                    systemctl is-active order-service
                '''

                echo 'All microservices are running successfully'
            }
        }
    }

    post {
        success {
            echo 'Microservices deployment successful!'
        }

        failure {
            echo 'Microservices deployment failed. Check the console log.'
        }
    }
}
