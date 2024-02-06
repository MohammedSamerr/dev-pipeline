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

        stage('Trigger config change'){
            steps{
                script{
                    sh """
                    curl -v -k -user mohamed_samer:11178c8b2c30d25cc8984c7065a4485990 -X POST -data `IMAGE_TAG=${TAG_NAME}` `http://localhost:8080/job/devcd-pipline/bbuildWithParameters?token=deptest-config`
                    """
                    
                }
            }
        }
    }
}


