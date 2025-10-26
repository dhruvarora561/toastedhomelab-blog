pipeline {
    agent {
        docker {
            image 'ruby:3.1'
            args '-u root'
        }
    }
    
    triggers {
        githubPush()
    }
    
    environment {
        JEKYLL_ENV = 'production'
        // If you need GitHub Pages deployment, add your token here
        // GITHUB_TOKEN = credentials('github-token')
    }
    
    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }
        
        stage('Setup Ruby and Bundler') {
            steps {
                sh '''
                    ruby --version
                    bundle --version
                    bundle install
                    bundle update
                '''
                // Cache gems (similar to GitHub Actions cache)
                script {
                    if (fileExists('.bundle')) {
                        stash name: 'bundle-cache', includes: '.bundle/**,vendor/bundle/**'
                    }
                }
            }
        }
        
        stage('SonarQube Analysis') {
            steps {
                script {
                    def scannerHome = tool 'sonarqube'
                    withSonarQubeEnv('sonarqube') {
                        sh "${scannerHome}/bin/sonar-scanner"
                    }
                }
            }
        }
        
        stage('Build Jekyll Site') {
            steps {
                sh '''
                    bundle exec jekyll clean
                    bundle exec jekyll build --trace
                '''
            }
            post {
                success {
                    echo "Jekyll build completed successfully"
                    archiveArtifacts artifacts: '_site/**', fingerprint: true
                }
            }
        }
        
        stage('Test Site') {
            steps {
                sh '''
                    # Test if build output exists and has content
                    if [ ! -d "_site" ]; then
                        echo "ERROR: _site directory not found"
                        exit 1
                    fi
                    
                    # Check if index.html exists
                    if [ ! -f "_site/index.html" ]; then
                        echo "ERROR: index.html not found in _site"
                        exit 1
                    fi
                    
                    echo "Site built successfully with $(find _site -type f | wc -l) files"
                '''
            }
        }
        
        stage('Deploy') {
            when {
                branch 'main'
            }
            steps {
                script {
                  echo "no deploy setup yet"
                }
            }
        }
    }
    
    post {
        always {
            echo "Pipeline ${currentBuild.result} for ${env.JOB_NAME} #${env.BUILD_NUMBER}"
            cleanWs()
        }
        success {
            script {
                // Update GitHub status to success
                githubNotify status: 'SUCCESS', description: 'Jekyll site built and deployed'
                emailext (
                    subject: "SUCCESS: Jekyll Site Deployed - ${env.JOB_NAME} #${env.BUILD_NUMBER}",
                    body: "Jekyll site successfully built and deployed.\nBuild URL: ${env.BUILD_URL}",
                    to: "your-email@example.com"
                )
            }
        }
        failure {
            script {
                // Update GitHub status to failure
                githubNotify status: 'FAILURE', description: 'Build failed'
                emailext (
                    subject: "FAILED: Jekyll Site Build - ${env.JOB_NAME} #${env.BUILD_NUMBER}",
                    body: "Jekyll site build failed. Please check the logs.\nBuild URL: ${env.BUILD_URL}",
                    to: "your-email@example.com"
                )
            }
        }
        unstable {
            script {
                githubNotify status: 'ERROR', description: 'Build unstable'
            }
        }
    }
}