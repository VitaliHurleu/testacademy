pipeline {
    agent {
        label 'Master'
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