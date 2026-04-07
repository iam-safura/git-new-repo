pipeline {
    agent any

    environment {
        EC2_USER = "ubuntu"
        EC2_HOST = "13.234.202.84"
        TARGET_DIR = "/var/www/html/"
    }

    stages {

        stage('Checkout Code') {
            steps {
                git 'https://github.com/iam-safura/cicdrepo.git'
            }
        }

        stage('Deploy with rsync') {
            steps {
                sshagent(['ec2-key-id']) {
                    sh """
                    rsync -avz --delete \
                      --exclude='.git' \
                      --exclude='Jenkinsfile' \
                      -e "ssh -o StrictHostKeyChecking=no" \
                      ./ ${EC2_USER}@${EC2_HOST}:${TARGET_DIR}
                    """
                }
            }
        }

        stage('Fix Permissions') {
            steps {
                sshagent(['ec2-key-id']) {
                    sh """
                    ssh -o StrictHostKeyChecking=no ${EC2_USER}@${EC2_HOST} '
                        sudo chown -R www-data:www-data /var/www/html &&
                        sudo chmod -R 755 /var/www/html
                    '
                    """
                }
            }
        }

    }
}