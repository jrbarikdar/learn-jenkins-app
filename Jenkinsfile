pipeline {
    agent any

    environment {
        NETLIFY_SITE_ID = '6f6fe66f-8079-4a39-809b-7e7fc9181471'
        NETLIFY_AUTH_TOKEN = credentials('netlify-token')
    }

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
                    ls -la
                    node --version
                    npm --version
                    npm ci
                    npm run build
                    ls -la
                '''
            }
        }
        stage('Tests') {
            parallel {
                stage('Unit Test') {
                    agent {
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
                }
                // stage('E2E') {
                //     agent {
                //         docker {
                //             image 'mcr.microsoft.com/playwright:v1.39.0-noble'
                //             reuseNode true
                //         }
                //     }
                //     steps {
                //         sh '''
                //             npm install serve
                //             node_modules/.bin/serve -s build &
                //             sleep 10
                //             npx playwright test --reporter=html
                //         '''
                //     }
                // }
            }
        }
        stage('Deploy') {
            agent {
                docker {
                    image 'node:18-alpine'
                    reuseNode true
                }
            }
            steps {
                sh '''
                    npm install netlify-cli@20.1.1
                    node_modules/.bin/netlify --version
                    echo "Deploying to Netlify site ID: $NETLIFY_SITE_ID"
                    node_modules/.bin/netlify status
                    node_modules/.bin/netlify deploy --dir=build --prod --site=$NETLIFY_SITE_ID
                '''
            }
        }
    }
    post {
        always {
            junit 'test-results/junit.xml'
        }
    }
}
