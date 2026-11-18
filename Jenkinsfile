
pipeline {

    agent any

    options {
        timestamps()
    }

    stages {

        stage('Checkout') {
            steps {
                echo 'Downloading source code from GitHub'
                checkout scm
            }
        }

        stage('Build') {
            steps {
                echo 'Validating application files'

                sh '''
                    test -f index.html
                    echo "Application files verified"
                '''
            }
        }

        stage('Deploy') {
            steps {

                sshagent(credentials: ['deployment-ssh']) {

                    sh '''
                        scp -o BatchMode=yes \
                            -o StrictHostKeyChecking=yes \
                            index.html \
                            deploy@172.31.1.53:/var/www/html/index.html

                        ssh -o BatchMode=yes \
                            -o StrictHostKeyChecking=yes \
                            deploy@172.31.1.53 \
                            "chmod 640 /var/www/html/index.html"
                    '''
                }
            }
        }

        stage('Verify') {
            steps {

                sshagent(credentials: ['deployment-ssh']) {

                    sh '''
                        ssh -o BatchMode=yes \
                            -o StrictHostKeyChecking=yes \
                            deploy@172.31.1.53 \
                            "curl -fsS http://localhost/"
                    '''
                }
            }
        }
    }

    post {
        success {
            echo 'Application deployed successfully!'
        }

        failure {
            echo 'Deployment failed. Check Console Output.'
        }
    }
}
