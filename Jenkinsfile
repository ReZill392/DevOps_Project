pipeline {
    agent any

    environment {
        NETLIFY_SITE_ID = '0f8a2c4f-2fe1-4f2f-936b-708fb150a9ac'
        NETLIFY_AUTH = credentials('netlify-token')
    }

    stages {
        stage('Install Dependencies') {
            agent {
                docker {
                    image 'node:18-alpine'
                    reuseNode true
                }
            }
            steps {
                sh '''
                    apk add --no-cache python3 py3-pip build-base
                    npm install netlify-cli
                '''
            }      
        }

        stage('Build') {
            agent {
                docker {
                    image 'node:18-alpine'
                    reuseNode true
                }
            }
            steps {
                echo "🔧 Checking required files..."
                sh '''
                    test -f index.html || (echo "❌ Missing index.html" && exit 1)
                    echo "✅ Build check passed."
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
                echo "🧪 Checking if function file exists..."
                sh '''
                    test -f ./netlify/functions/uiia.png && echo "✅ File exists" || (echo "❌ File missing" && exit 1)
                '''
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
                echo "🚀 Deploying to Netlify..."
                sh '''
                    npm install netlify-cli
                    node_modules/.bin/netlify deploy \
                    --auth=$NETLIFY_AUTH \
                    --site=$NETLIFY_SITE_ID \
                    --dir=. \
                    --prod
                '''
            }
        }

        stage('Post Deploy') {
            steps {
                echo "✅ Deployment complete! Your app is live."
            }
        }
    } // ✅ ปิด stages ตรงนี้

    post {
        success {
            echo "🎉 CI/CD pipeline finished successfully."
        }
        failure {
            echo "❌ Pipeline failed. Check logs for details."
        }
    }
}
