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
                sh '''
                    docker run --name juice-shop -d -p 3000:3000 bkimminich/juice-shop:latest
                '''
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
            post{
                always{
                    sh '''
                        pwd
                        mkdir results
                        docker cp zap:/zap/wrk/reports/zap_html_report.html results/zap_html_report.html
                        docker cp zap:/zap/wrk/reports/zap_xml_report.xml results/zap_xml_report.xml
                        docker rm zap
                    '''
                }
            }
        }
        stage('OSV scanner'){
            steps{
                sh '''
                    pwd
                    osv-scanner scan --lockfile package-lock.json --format json --output results/sca-osv-scanner.json on  || true
                '''
            }
            post {
                always {
                    sh '''
                        pwd
                        docker stop juice-shop
                        docker rm juice-shop
                    '''
                }
            }
        }
        stage('TruffleHog'){
            steps{
                git clone --mirror https://github.com/lm-abcd/abcd-student.git
                sh '''
                    trufflehog git file://. --only-verified --bare --json --no-update > results/TruffleHog_report.json
                '''
            }
            post {
                always {
                    echo 'Archiving results...'
                    archiveArtifacts artifacts: 'results/**/*', fingerprint: true, allowEmptyArchive: true
                }
            }
        }
    }
}
