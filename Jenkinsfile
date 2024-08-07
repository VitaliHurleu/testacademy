pipeline {
    agent {
        label 'Master'
    }
    environment {
        DOCKERHUB_TOKEN = credentials('dockerhub_token')
        imagename = "barabanuser02/testacademy"
        containername = "my-container"
        gpg_secret = credentials("gpg-secret")
        gpg_trust = credentials("gpg-ownertrust")
        gpg_passphrase = credentials("gpg-passphrase")
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
                sh 'curl google.com'
                sh 'docker stop -d "${containername}"'
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
            steps{
                echo 'deploying ' 
            }
        }
    }
}
