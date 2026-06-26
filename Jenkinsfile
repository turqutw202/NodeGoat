pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('PAST Scan (npm audit)') {
            steps {
                echo 'Kənar kitabxanaların təhlükəsizlik analizi (PAST) başladılır...'
                sh 'docker run --rm -v $(pwd):/app -w /app node:lts npm audit --json > past-report.json || true'
            }
        }

        stage('SAST Scan (NjSSCAN)') {
            steps {
                echo 'Mənbə kodunun statik analizi (SAST) başladılır...'
                sh 'docker run --rm -v $(pwd):/src opensecurity/njsscan /src --sarif -o /src/sast-report.sarif || true'
            }
        }

        stage('DAST Scan (OWASP ZAP)') {
            steps {
                echo 'Canlı işləyən saytın dinamik analizi (DAST) başladılır...'
                sh 'docker run --rm -v $(pwd):/zap/wrk:rw -t ghcr.io/zaproxy/zaproxy:stable zap-baseline.py -t http://16.16.162.119:4000 -g gen.conf -r dast-report.html || true'
            }
        }
    }
}
