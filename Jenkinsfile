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
                sh '''
                docker run --name zap --add-host="host.docker.internal:host-gateway" \
                -v /home/nblmaslanka/DEVSECOPS/abcd-lab-master/abcd-lab-master/resources/DAST/zap:/zap/wrk/:rw \
                -t ghcr.io/zaproxy/zaproxy:stable bash -c \
                "zap.sh -cmd -addonupdate; zap.sh -cmd -addoninstall communityScripts -addoninstall pscanrulesAlpha -addoninstall pscanrulesBeta -autorun /zap/wrk/passive_scan.yaml" || true
                '''
            }
        }
        stage('Stop Juice Shop'){
            steps{
                sh '''
                docker cp zap:/zap/wrk/zap_html_report.html /var/jenkins_home/workspace/reports/1/zap_html_report.html'
                docker cp zap:/zap/wrk/zap_xml_report.xml /var/jenkins_home/workspace/reports/1/zap_xml_report.xml'
                docker stop juice-shop
                docker rm zap
                '''
            }
        }
    }
}
