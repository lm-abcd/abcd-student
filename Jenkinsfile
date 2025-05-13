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
                sh 'pwd'
            }
        }
        stage('Juice Shop'){
            steps{
                sh 'docker run --rm --name juice-shop -d -p 3000:3000 bkimminich/juice-shop:latest'
            }
        }
        stage('ZAP pasive scan'){
            steps{
                sh '/usr/bin/bash /home/nblmaslanka/DEVSECOPS/abcd-lab-master/abcd-lab-master/resources/DAST/zap/run_passive_scan.sh'
            }
        }
        stage('Stop Juice Shop'){
            steps{
                sh 'docker stop juice-shop'
            }
        }
    }
}
