pipeline {

    agent any

    environment{
        DOCKERHUB_USERNAME = "anastobe"
        APP_NAME = "gitops-argo-app"
        IMAGE_TAG = "${BUILD_NUMBER}"
        IMAGE_NAME = "${DOCKERHUB_USERNAME}" + "/" + "${APP_NAME}"  
        REGISTRY_CREDS = 'dockerhub'
    }
    
    stages{

        stage("Cleanup workspace"){

            steps{
                script{

                    cleanWs()
                }
            }
        }
        stage('Checkout SCM'){
            steps{
                script{
                   git credentialsId: 'github',
                   url: 'https://github.com/anastobe/gitops_argocd_project.git',
                   branch: 'main'
                }
            }
        }
        stage('Build Docker Image'){
                steps{
                    script{
                        docker_image = docker.build "${IMAGE_NAME}"
                    }
                }
            }
        stage('Push Docker Image') {
            steps {
                script {
                    docker.withRegistry('', REGISTRY_CREDS) {
                        docker_image.push("${BUILD_NUMBER}")
                        docker_image.push('latest')
                    }
                }
            }
        } 
        stage('Delete Docker images') {
            steps {
                script {
                    sh "docker rmi ${IMAGE_NAME}:${IMAGE_TAG}"
                    sh "docker rmi ${IMAGE_NAME}:latest"
                }
            }
        }
        stage('Updating kubernetes deployment file') {
            steps {
                script {
                    sh """
                    cat deployment.yml
                    sed -i 's/${APP_NAME}.*/${APP_NAME}:${IMAGE_TAG}/g' deployment.yml
                    cat deployment.yml
                    """
                }
            }
        }

        stage('Push the chenged deployment file to Git') {
            steps {
                script {
                    sh """
                    git config --global user.name "anastobe"
                    git config --global user.email "anastobe968@gmail.com"
                    git add deployment.yml
                    git commit -m "updated the deployment file"
                    """
                    withCredentials([gitUsernamePassword(credentialsId: 'github', gitToolName: 'Default')]) {
                       sh "git push https://github.com/anastobe/gitops_argocd_project.git main"
                    }

                }
            }
        }

    }
}
//3rd
//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
//2nd latest
//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
//1st
//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