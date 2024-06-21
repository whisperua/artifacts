pipeline {
    agent any

    environment {
        SUDO_PASSWORD = credentials('SUDO_PASSWORD') 
    }

    stages {
        stage('Update Packages') {
            steps {
                script {
                    // Update the package list
                    sh 'echo $SUDO_PASSWORD | sudo -S apt-get update'
                }
            }
        }

        stage('Install Apache2') {
            steps {
                script {
                    // Install Apache2
                    sh 'echo $SUDO_PASSWORD | sudo -S apt-get install -y apache2'
                }
            }
        }

        stage('Start Apache2') {
            steps {
                script {
                    sh 'echo $SUDO_PASSWORD | sudo -S systemctl start apache2'
                }
            }
        }

        stage('Check Apache Logs') {
            steps {
                script {
                    // Read the log file and check for 4xx and 5xx errors
                    def errors = sh (
                        script: "grep -E 'HTTP/[0-9]+.[0-9]+\" [4-5][0-9]{2}' /var/log/apache2/access.log",
                        returnStatus: true
                    )
                    if (errors != 0) {
                        echo "No 4xx or 5xx errors found in the log."
                    } else {
                        echo "Found 4xx or 5xx errors in the log."
                        error "Pipeline failed due to presence of 4xx or 5xx errors in the log."
                    }
                }
            }
        }
    }

    post {
        always {
            echo 'Pipeline execution completed ^_^'
        }
    }
}
