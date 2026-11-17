
pipeline {
    agent any

    stages {
        stage('Build') {
            agent {
                docker {
                    image 'node:18-alpine'
                    reuseNode true
                }
            }
            steps {
                sh '''
                    set -e

                    echo "Checking Node and npm"
                    node --version
                    npm --version

                    echo "Configuring Zscaler certificate"
                    if [ -f /usr/local/share/ca-certificates/zscaler-root-ca.crt ]; then
                        export NODE_EXTRA_CA_CERTS=/usr/local/share/ca-certificates/zscaler-root-ca.crt
                    fi

                    echo "Installing dependencies"
                    npm ci --no-audit --no-fund

                    echo "Building React application"
                    npm run build

                    echo "Checking build output"
                    ls -la build/
                '''
            }
        }
    }
}