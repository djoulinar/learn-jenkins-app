
pipeline {
    agent any

    environment {
        NETLIFY_SITE_ID = '535f0c12-9621-4999-84a4-774778234707'
        NETLIFY_AUTH_TOKEN = credentials('netlify-token')
    }

    stages {
        stage('build') {
            agent {
                docker {
                    image 'node:18-alpine'
                    reuseNode true
                }
            }
            steps {
                sh '''
                    ls -ls
                    node --version
                    npm --version
                    npm ci
                    npm run build
                    ls -ls
                '''
            }
        }

        stage('test') {
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

        stage('deploy') {
            agent {
                docker {
                    image 'node:18-alpine'
                    reuseNode true
                    args '-u root'
                }
            }
            steps {
                sh '''
                    apk add --no-cache bash
                    npm install netlify-cli

                    node_modules/.bin/netlify --version

                    echo "Deploying to production..."

                    node_modules/.bin/netlify deploy \
                        --prod \
                        --dir=build \
                        --site=$NETLIFY_SITE_ID \
                        --auth=$NETLIFY_AUTH_TOKEN
                    node_modules/.bin/netlify deploy --dir=build --prod
                '''
            }
        }
    }
}

