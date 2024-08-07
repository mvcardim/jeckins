pipeline {
    agent any
    environment {
        KUBECONFIG = "/var/lib/jenkins/.kube/config"
    }
    stages {
        stage('Build Image') {
            steps {
                script {
                    dockerapp = docker.build("mvcardim/jeckins:${env.BUILD_ID}", '-f ./src/Dockerfile ./src')
                }
            }
        }
        stage('Push Image') {
            steps {
                script { 
                    docker.withRegistry('https://registry.hub.docker.com', 'dockerhub') {
                        dockerapp.push('latest')
                        dockerapp.push("${env.BUILD_ID}")
                    }
                }
            }
        }
        stage('Deploy Kubernetes') {
            environment{
                tag_version = "${env.BUILD_ID}" 
            }
            steps {
                 witKubeConfig([credentialsId: 'kubeconfig']){
                    sh 'sed -i "s/{{tag}}/$tag_version/g" ./home/marco/jeckins/Jeckins/k8s/deployment.yaml'
                    sh 'kubectl apply -f ./k8s/deployment.yaml'
                 }
                
            }
        }
    }
}


