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
                    git credentialsId: 'github-pat', url: 'https://github.com/wojciech-trawinski-xtb/abcd-student', branch: 'main'
                }
            }
        }
        stage('Example') {
            steps {
                echo 'Hello!'
                sh 'ls -la'
            }
        }
        // stage('[ZAP] Baseline passive-scan') {
        //     steps {
        //         sh 'mkdir -p results/'
        //         sh '''
        //             docker run --name juice-shop -d --rm \
        //             -p 3000:3000 \
        //             bkimminich/juice-shop
        //             sleep 5
        //         '''
        //         sh '''
        //             docker run --name zap \
        //             --add-host=host.docker.internal:host-gateway \
        //             -v /Users/wtrawinski/workspace/abcd/abcd-student/.zap:/zap/wrk/:rw \
        //             -t ghcr.io/zaproxy/zaproxy:stable bash -c \
        //             "zap.sh -cmd -addonupdate; zap.sh -cmd -addoninstall communityScripts -addoninstall pscanrulesAlpha -addoninstall pscanrulesBeta -autorun /zap/wrk/passive.yaml" || true
        //         '''
        //     }
        //     post {
        //         always {
        //             sh '''
        //                 docker cp zap:/zap/wrk/reports/zap_html_report.html ${WORKSPACE}/results/zap_html_report.html
        //                 docker cp zap:/zap/wrk/reports/zap_xml_report.xml ${WORKSPACE}/results/zap_xml_report.xml
        //                 docker stop zap juice-shop
        //                 docker rm zap
        //             '''
        //         }
        //     }
        // }
        // stage('OSV scanner') {
        //     steps {
        //         sh 'mkdir -p results/'
        //         sh 'osv-scanner scan --lockfile package-lock.json --format json --output results/osv_report.json || true' 
        //     }
        // }
        // stage('TruffleHog') {
        //     steps {
        //         sh 'mkdir -p results/'
        //         sh 'trufflehog git file://. --only-verified --json > results/truffle_hog_report.json' 
        //     }
        // }
        stage('Semgrep') {
            steps {
                sh 'mkdir -p results/'
                sh 'semgrep scan --config auto --json > results/semgrep_report.json' 
            }
        }
    }
    post {
        always {
            archiveArtifacts artifacts: 'results/**/*', fingerprint: true, allowEmptyArchive: true
            // defectDojoPublisher(artifact: 'results/zap_xml_report.xml', productName: 'Juice Shop', scanType: 'ZAP Scan', engagementName: 'wojciech.trawinski@xtb.com')
            // defectDojoPublisher(artifact: 'results/osv_report.json', productName: 'Juice Shop', scanType: 'OSV Scan', engagementName: 'wojciech.trawinski@xtb.com')
            // defectDojoPublisher(artifact: 'results/truffle_hog_report.json', productName: 'Juice Shop', scanType: 'Trufflehog Scan', engagementName: 'wojciech.trawinski@xtb.com')
            defectDojoPublisher(artifact: 'results/semgrep_report.json', productName: 'Juice Shop', scanType: 'Semgrep JSON Report', engagementName: 'wojciech.trawinski@xtb.com')
        }
    }
}
