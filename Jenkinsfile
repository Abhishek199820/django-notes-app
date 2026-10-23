pipeline {
    agent {
        label "agent"
    }

    stages {
        stage("Code") {
            steps {
                echo "This stage is for cloning the project from GitHub"

                deleteDir()

                git(
                    url: "https://github.com/Abhishek199820/django-notes-app.git",
                    branch: "main"
                )

                echo "Code cloned successfully"
            }
        }

        stage("Build") {
            steps {
                echo "This stage is for building the project"

                sh "whoami"
                sh "docker build -t notes-app:latest ."
            }
        }

        stage("Push to Docker") {
            steps {
                echo "In this stage the image will be pushed to Docker Hub"

                withCredentials([
                    usernamePassword(
                        credentialsId: "dockerHubCred",
                        usernameVariable: "dockerHubUser",
                        passwordVariable: "dockerHubPass"
                    )
                ]) {
                    sh '''
                        set +x
                        echo "$dockerHubPass" | docker login \
                            --username "$dockerHubUser" \
                            --password-stdin

                        docker image tag notes-app:latest \
                            "$dockerHubUser/notes-app:latest"

                        docker push "$dockerHubUser/notes-app:latest"

                        docker logout
                    '''
                }

                echo "Docker image pushed successfully"
            }
        }

        stage("Deploy") {
            steps {
                echo "This is the final stage for deploying the application"

                sh "docker compose up -d"

                // Add AKS deployment commands here when required.
            }
        }
    }

    post {
        success {
            echo "Pipeline completed successfully"
        }

        failure {
            echo "Pipeline failed. Check the console output for details."
        }

        always {
            echo "Pipeline execution finished"
        }
    }
}
