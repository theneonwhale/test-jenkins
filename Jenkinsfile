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
                sh '''
                # Read Apache logs and check for 4xx/5xx errors
                if grep -E "HTTP/1.[01]\" (4[0-9]{2}|5[0-9]{2})" /var/log/httpd/access_log; then
                    echo "Errors found in the logs!"
                    exit 1
                else
                    echo "No 4xx or 5xx errors found."
                fi
                '''
            }
        }
    }
    
    post {
        always {
            archiveArtifacts artifacts: 'logs/**/*', allowEmptyArchive: true
        }
    }
}
