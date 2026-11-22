
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
                    set -eu

                    echo "Checking Node and npm"
                    node --version
                    npm --version

                    echo "Installing dependencies"
                    npm ci --no-audit --no-fund --verbose

                    echo "Checking react-scripts"
                    npm ls react-scripts

                    echo "Building application"
                    npm run build
                '''
            }
        }

        stage('Test') {
            agent {
                docker {
                    image 'node:18-alpine'
                    reuseNode true
                }
            }

            steps {
                sh '''
                    set -eu

                    echo "Running application tests"
                    CI=true npm test -- --watchAll=false
                '''
            }

            post {
                always {
                    junit 'test-results/junit.xml'
                }
            }
        }

        stage('Archive Artifacts') {
            steps {
                archiveArtifacts artifacts: 'build/**',
                                 fingerprint: true
            }
        }
    }
}
