pipeline {
    agent any

    environment {
        APP_ENV = 'development'  // Define environment variables
        BUILD_DIR = 'build'
        DEPLOY_TARGET = 'staging-server'
    }
    
    options {
        timeout(time: 30, unit: 'MINUTES')  // Set a timeout for the entire pipeline
        retry(2)  // Retry the pipeline 2 times in case of failure
    }
    
    stages {
        stage('Checkout') {
            steps {
                echo 'Checking out the source code...'
                git url: 'https://github.com/11KAlYAN11/JenkinsDemoRepo.git', branch: 'main'
            }
        }

        stage('Build') {
            steps {
                script {
                    echo 'Building the project...'
                    //sh 'mkdir -p ${BUILD_DIR} && cd ${BUILD_DIR} && javac HelloWorld.java'  // Example shell command
                    sh 'javac HelloWorld.java'
                }
            }
        }

        stage('Test') {
            parallel {
                stage('Unit Tests') {
                    steps {
                        echo 'Running Unit Tests...'
                        sh 'echo "Running unit tests"'
                    }
                }
                stage('Integration Tests') {
                    steps {
                        echo 'Running Integration Tests...'
                        sh 'echo "Running integration tests"'
                    }
                }
            }
        }
        
        stage('Deploy') {
            when {
                expression { return env.APP_ENV == 'production' }  // Only deploy if environment is production
            }
            steps {
                echo 'Deploying to target: ' + env.DEPLOY_TARGET
                //sh 'scp -r ${BUILD_DIR} user@${DEPLOY_TARGET}:/var/www/app'
            }
        }
    }
    
    post {
        always {
            echo 'Cleaning up...'
            sh 'rm -rf ${BUILD_DIR}'  // Clean up the build directory
        }
        
        success {
            echo 'Pipeline completed successfully!'
            archiveArtifacts artifacts: '**/target/*.jar', allowEmptyArchive: true  // Archive build artifacts
            junit '**/target/test-results/*.xml'  // Publish test results
        }
        
        failure {
            echo 'Pipeline failed.'
            mail to: 'programmer143143@gmail.com',
                 subject: "Failed Pipeline: ${currentBuild.fullDisplayName}",
                 body: "Something is wrong with pipeline ${currentBuild.fullDisplayName}. Please check it out."
        }

        unstable {
            echo 'Pipeline is unstable.'
        }

        changed {
            echo 'Pipeline result has changed from the previous build.'
        }
    }
}
