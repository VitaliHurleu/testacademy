pipeline {
    agent {
        label 'Master'
    }
    environment {
        DOCKERHUB_TOKEN = credentials('dockerhub_token')
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
        stage('Build'){
            steps{
                echo 'building...'
            }
        }
        stage('Test image') {
            steps {
                echo 'testing...'
            }
        }
        
        stage('Push'){
            steps{
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
