pipeline{
    agent any
    stages{
        stage("Clonning git repo"){
            steps{
                git url:'https://github.com/priym14/two-tier-flask-app.git', branch: 'main'
            }
            }
         stage("Building the Docker image"){
            steps{
                echo "Building the Docker image"
                sh 'docker build -t two-tier-app .'
            }
        }
        stage("Logging and pushing the image to Docker hub"){
            steps{
                withCredentials([usernamePassword(credentialsId:"dockerHub",passwordVariable:"dockerHubPass",usernameVariable:"dockerHubUser")]){
                echo "tagging the docker image"
                sh 'docker tag two-tier-app $dockerHubUser/two-tier-app:$BUILD_NUMBER'
                echo "logging into the Docker image"
                sh 'docker login -u $dockerHubUser -p $dockerHubPass'
                echo "pushing the Docker image"
                sh 'docker push $dockerHubUser/two-tier-app:$BUILD_NUMBER'
                }
            }
        }
        stage("Deploy to EKS"){
            steps {
                echo "Deploying the container to EKS"
                withKubeConfig(caCertificate: '', clusterName: 'webapp', contextName: '', credentialsId: 'k8s', namespace: '', restrictKubeConfigAccess: false, serverUrl: '') {
                sh 'kubectl apply --validate=false -f eks-manifests/mysql-configmap.yml'
                sh 'kubectl apply --validate=false -f eks-manifests/mysql-deployment.yml'
                sh 'kubectl apply --validate=false -f eks-manifests/mysql-secrets.yml'
                sh 'kubectl apply --validate=false -f eks-manifests/mysql-svc.yml'
                sh 'kubectl apply --validate=false -f eks-manifests/two-tier-app-deployment.yml'
                sh 'kubectl apply --validate=false -f eks-manifests/two-tier-app-svc.yml'
}
                
                
            }
        } 
        }
    }
