pipeline {
    agent any

    stages {
        stage('Install Apache') {
            steps {
                sh '''
                # Install Apache (httpd)
                sudo dnf install -y httpd
                sudo systemctl start httpd
                sudo systemctl enable httpd
                '''
            }
        }

        stage('Check Logs for 4xx and 5xx Errors') {
            steps {
                script {
                    // Check for 4xx and 5xx errors in the Apache access log
                    def errorsFound = sh(script: '''
                        if grep -E "HTTP/1.[01]\" (4[0-9]{2}|5[0-9]{2})" /var/log/httpd/access_log; then
                            echo "Errors found in the logs!"
                            exit 1
                        else
                            echo "No 4xx or 5xx errors found."
                            exit 0
                        fi
                    ''', returnStatus: true)

                    // If errors are found, fail the build
                    if (errorsFound != 0) {
                        error("4xx or 5xx errors detected in Apache logs.")
                    }
                }
            }
        }
    }

    post {
        always {
            archiveArtifacts artifacts: 'logs/**/*', allowEmptyArchive: true
        }
    }
}

