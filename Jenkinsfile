
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
                    user-service/app.py \
                    product-service/app.py \
                    order-service/app.py
                '''

                echo 'Build completed successfully'
            }
        }

        stage('Deploy') {
            steps {
                echo 'Deploying microservices to AWS EC2'

                sh '''
                    set -eu

                    cp user-service/app.py \
                    /opt/microservices/user-service/app.py

                    cp product-service/app.py \
                    /opt/microservices/product-service/app.py

                    cp order-service/app.py \
                    /opt/microservices/order-service/app.py
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
