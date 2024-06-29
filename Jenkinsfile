pipeline {
    agent any

    environment {
        NETLIFY_SITE_ID = '739462b5-502e-421d-8656-9566c0080f07'
    }

    stages {

        stage('build') {
            agent{
                docker {
                    image 'node:18-alpine'
                    reuseNode true
                }
            }
            steps {
                sh '''
                    ls -la
                    node --version
                    npm --version
                    npm ci 
                    npm run build
                '''

            }
        }

        stage('Tests') {
            parallel{
                    stage('Unit Test') {
                                agent{
                                    docker {
                                        image 'node:18-alpine'
                                        reuseNode true
                                    }
                                }

                                steps {
                                    sh '''
                                    test -f build/index.html
                                    npm test    
                                    '''
                                }

                                 post {
                                        always {
                                            junit 'jest-results/junit.xml'
                                            
                                        }
                                    }
                            }

                            stage('E2E') {
                                agent{
                                    docker {
                                        image 'mcr.microsoft.com/playwright:v1.39.0-jammy'
                                        reuseNode true
                                    }
                                }

                                steps {
                                    sh '''
                                        npm install serve
                                        node_modules/.bin/serve -s build &
                                        sleep 10
                                        npx playwright test --reporter=html
                                    '''
                                }
                            }
            }
        }

        stage('Deploy') {
            agent{
                docker {
                    image 'node:18-alpine'
                    reuseNode true
                }
            }
            steps {
                sh '''
                   npm install netlify-cli
                   node_modules/.bin/netlify --version
                   echo "Deplying to production. Site ID: $NETLIFY_SITE_ID"
                '''

            }
        } 
    }
}
