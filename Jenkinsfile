pipeline{
    agent any

    environment{
        DOCKERHUB_USERNAME = "mohamedsamir12"
        APP_NAME= "git-ops-pipeline"
        TAG_NAME = "${BUILD_NUMBER}"
        IMAGE_NAME= "${DOCKERHUB_USERNAME}" + "/" + "${APP_NAME}"
        REGISTRY_CREDS = "dockerhub"
    }

    stages{
        stage('Cleanup workspace'){
            steps{
                script{
                    cleanWs()
                }
            }
        }
        stage('Checkout SCM'){
            steps{
                script{
                    git credentialsId : 'github',
                    url: 'https://github.com/MohammedSamerr/dev-pipeline.git',
                    branch: 'master'
                }
            }
        }

        stage('Build docker image'){
            steps{
                script{
                    docker_image = docker.build "${IMAGE_NAME}" 
                }
            }
        }

        stage('Push Docker Image'){
            steps{
                script{
                    //first => login to dockerhub
                    // help jenkins to authenticate with dockerhub
                    docker.withRegistry('',REGISTRY_CREDS){
                        docker_image.push("$BUILD_NUMBER")
                        docker_image.push('latest')
                    }
                }
            }

        }

        stage('Delete Docker image'){
            steps{
                script{
                    sh "docker rmi ${IMAGE_NAME}:${TAG_NAME}"
                    sh "docker rmi ${IMAGE_NAME}:latest"
                }
            }
        }
        
        stage('Updating kubernetes deployment file'){
            steps{
                script{
                        sh """
                            cat deplument.yaml
                            sed -i 's/${APP_NAME}.*/${APP_NAME}:${TAG_NAME}/g' deplument.yaml
                            cat deplument.yaml
                        """
                }
            }
        }
    }
}