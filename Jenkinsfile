pipeline {
    agent any
    options {
        skipDefaultCheckout(true)
    }
    stages {
        stage('Code checkout from GitHub') {
            steps {
                script {
                    cleanWs()
                    git credentialsId: 'github-token', url: 'https://github.com/lm-abcd/abcd-student.git', branch: 'main'
                }
            }
        }
        stage('Example') {
            steps {
                echo 'Hello!'
                sh 'ls -la'
            }
        }
        stage('Juice Shop'){
            steps{
                sh 'sudo docker run --rm --name juice-shop -d -p 3000:3000 bkimminich/juice-shop:latest'
            }
        }
    }
}
