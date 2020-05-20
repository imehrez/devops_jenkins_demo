pipeline {
    agent { docker { image 'python:3.5.1' } }
    
    environment {
        DISABLE_AUTH = 'true'
        DB_ENGINE    = 'sqlite'
    }

    stages {
        stage('Build') {
            steps {
                retry(3) {
                    sh './build.sh'
                }

                echo "Database engine is ${DB_ENGINE}"
                echo "DISABLE_AUTH is ${DISABLE_AUTH}"

            }
        }

        stage('Test') {
            steps {
                timeout(time: 3, unit: 'MINUTES') {
                    sh './test.sh'
                }
            }
        }

        stage('Deploy') {
            steps {
                retry(3) {
                    sh './deploy.sh'
                }
            }
        
    }
    post {
        always {
            echo 'This will always run'
            deleteDir() /* clean up our workspace */
        }
        success {
            echo 'This will run only if successful'
            slackSend channel: '#devops-testing',
                  color: 'good',
                  message: "The pipeline ${currentBuild.fullDisplayName} completed successfully."
        }
        failure {
            echo 'This will run only if failed'
        }
        unstable {
            echo 'This will run only if the run was marked as unstable'
        }
        changed {
            echo 'This will run only if the state of the Pipeline has changed'
            echo 'For example, if the Pipeline was previously failing but is now successful'
        }
    }
}
