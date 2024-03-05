pipeline {
    agent any
   
    environment {
        PROJECT_ID = "deal-cart-prod"
        CLUSTER_NAME = "dev-cluster"
        LOCATION = "asia-southeast1-c"
        CREDENTIALS_ID = 'kubernetes'
    }
    stages {
        stage('pull from github repo'){
            steps{
                git "https://github.com/sehargithub/practice-code.git"
            }
        }
        
        stage('build docker image'){
            steps{
                script{
                sh "docker build -t dealcart/buksapp-frontend:${env.BUILD_ID} ."                
            }
        }

        }


        
        stage('push docker image to dockerhub'){
            steps{
        withCredentials([string(credentialsId: 'dockerhub', variable: 'dockerhub')]) {
            echo "Docker Hub credentials: ${dockerhub}" // Debug output
            sh "docker login -u dealcart -p ${dockerhub} docker.io"
        }
        sh "docker push dealcart/buksapp-frontend:${env.BUILD_ID}"
    }
}

        stage('deploy on k8 cluster'){
            steps{
                sh "sed -i 's/tagversion/${env.BUILD_ID}/g' frontend-k8.yaml"
                step([$class: 'KubernetesEngineBuilder', projectId: env.PROJECT_ID, clusterName: env.CLUSTER_NAME, location: env.LOCATION, manifestPattern: 'frontend-k8.yaml', credentialsId: env.CREDENTIALS_ID, verifyDeployments: true])   
            }
        }
    }
}