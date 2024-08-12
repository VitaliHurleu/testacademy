pipeline {
    agent {
        label 'Master'
    }
    environment {
        DOCKERHUB_TOKEN = credentials('dockerhub_token')
        imagename = "barabanuser02/testacademy"
        containername = "my-container"
    }
    stages{
        stage('Checkout'){
            steps{
                git url: 'https://github.com/VitaliHurleu/testacademy.git', branch: 'main'
            }
        }
        stage ("Quality Gate") {
            parallel {
                stage ("Dockerfile") {
                    agent {
                        docker {
                            image 'hadolint/hadolint:latest-debian'
                        }
                    }
                steps {
                    sh 'hadolint Dockerfile | tee -a      ms1_docker_lint.txt'
                }
                    post {
                        always {
                           archiveArtifacts 'ms1_docker_lint.txt'
                        }
                    }
                }
            }
        }    
        stage('Build'){
            steps{
                sh 'docker build . -t "${imagename}:latest"'
                echo 'building...'
            }
        }
        stage('Test image') {
            steps {
                sh 'docker inspect --type=image "${imagename}:latest" '
                sh 'docker run -d -p 3333:3000 --name "${containername}" "${imagename}:latest"'
                sh 'sleep 5'
                sh 'curl localhost:3333'
                sh 'docker stop "${containername}"'
                sh 'docker rm "${containername}"'
                echo 'testing...'
            }  
        }     
        stage('Push'){
            steps{
                sh 'echo $DOCKERHUB_TOKEN_PSW | sudo docker login -u $DOCKERHUB_TOKEN_USR --password-stdin'
                sh 'sudo docker push "${imagename}:latest"'
                echo 'pushing...'
            }
        }  
        stage('Deploy'){
            steps {
                script {
                    withKubeConfig([credentialsId: 'minikubeconfig']) {
                        sh 'kubectl config set-context --current --namespace=test'
                        sh 'kubectl apply -f deployment.yaml -f serviceNP.yaml'
                        sh 'sleep 5'
                        sh 'kubectl rollout status deployment k8s-testacademy'
                    }
                    env.selected_environment = input  message: 'Test pre-production is ok, select environment to Deploy',ok : 'Proceed',id :'tag_id',
                    parameters:[choice(choices: ['not deploy', 'prod'], description: 'Select environment', name: 'env')]
                    if (env.selected_environment == "prod"){
                        withKubeConfig([credentialsId: 'minikubeconfig']) {
                            sh 'kubectl config set-context --current --namespace=prod'
                            sh 'kubectl apply -f deployment.yaml -f serviceLB.yaml'
                        }
                        echo "Deploying in ${env.selected_environment}."
                    }
                    withKubeConfig([credentialsId: 'minikubeconfig']) {
                            sh 'kubectl delete --namespace=test deploy/k8s-testacademy'
                            sh 'kubectl delete --namespace=test svc/k8s-testacademy'
                        }
                }

            }
        }
    }
}
