
pipeline {
    agent any

    stages {
        stage('Build') {
            agent {
                docker {
                    image 'node:18-alpine'
                    reuseNode true
                    args '-v /certs/zscaler-root-ca.pem:/tmp/zscaler-root-ca.pem:ro'
                }
            }

            steps {
                sh '''
                    set -eu

                    echo "Checking Node and npm"
                    node --version
                    npm --version

                    echo "Configuring Zscaler certificate"
                    export NODE_EXTRA_CA_CERTS=/tmp/zscaler-root-ca.pem

                    echo "Checking certificate"
                    test -f "$NODE_EXTRA_CA_CERTS"

                    echo "Installing dependencies"
                    if ! npm ci --no-audit --no-fund --verbose; then
                        echo "npm ci failed. Showing latest npm log:"
                        log_file=$(ls -t /home/node/.npm/_logs/* 2>/dev/null | head -n 1 || true)
                        if [ -n "$log_file" ]; then
                            tail -n 100 "$log_file"
                        fi
                        exit 1
                    fi

                    echo "Building React application"
                    npm run build

                    echo "Checking build output"
                    ls -la build/
                '''
            }
        }
    }
}
