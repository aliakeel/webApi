pipeline {
    agent any

    environment {
        DOTNET_CLI_HOME = "C:\\Program Files\\dotnet"
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build') {
            steps {
                script {
                    // Restoring dependencies
                    //bat "cd ${DOTNET_CLI_HOME} && dotnet restore"
                    bat "dotnet restore"

                    // Building the application
                    bat "dotnet build --configuration Release"
                }
            }
        }

        stage('Test') {
            steps {
                script {
                    // Running tests
                    bat "dotnet test --no-restore --configuration Release"
                }
            }
        }
        
        stage('Docker') {
            steps {
                script {
                    // make  sure we have a tag to use for docker image. If not, then fail the job
                    bat "docker build -t TestWebApplication-docker-published ./TestWebApplication"

                    // save docker image aas tar file
                    bat "docker save -o TestWebApplication-docker.tar TestWebApplication-docker-published"
                    
                    // run container from saved image and check if it is running
                    bat "docker run --name TestWebApplication-app-container -d -p 8798:80 TestWebApplication-docker-published"
                    
                }
            }
        }

        stage('Publish') {
            steps {
                script {
                    // Publishing the application
                    bat "dotnet publish --no-restore --configuration Release --output .\\publish"
                }
            }
        }
    }

    post {
        success {
            echo 'Build, test, and publish successful!'
        }
    }
}
