
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

                    echo "Checking Zscaler certificate"
                    if [ -f /usr/local/share/ca-certificates/zscaler-root-ca.crt ]; then
                        echo "Zscaler certificate found"
                        export NODE_EXTRA_CA_CERTS=/usr/local/share/ca-certificates/zscaler-root-ca.crt
                    else
                        echo "Zscaler certificate NOT found"
                    fi

                    echo "Installing dependencies"
                    if ! npm ci --no-audit --no-fund; then
                        echo "npm installation failed. Latest npm log:"
                        ls -t /home/node/.npm/_logs/* | head -1 | xargs tail -n 60
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
