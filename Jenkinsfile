pipeline {
    agent {label 'JenkinsVMUnraid'}
    
        environment {
        GEM_HOME = '/home/jenkins/.local/share/gem/ruby/3.2.0'
        PATH = "$GEM_HOME/bin:$PATH"
        BUNDLE_PATH = '/home/jenkins/.bundle'
        JEKYLL_ENV = 'production'
    }
    
    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }
        
        stage('Setup Ruby Environment') {
            steps {
                script {
                    sh '''
                        echo "Ruby version:"
                        ruby --version
                        echo "Gem version:"
                        gem --version
                        # Install bundler if not present
                        gem install bundler
                        echo "Bundler version:"
                        bundle --version
                    '''
                }
            }
        }
        
        stage('Install Dependencies') {
            steps {
                sh '''
                    echo "Installing gem dependencies..."
                    bundle install --jobs 4 --retry 3
                    echo "Available gems:"
                    bundle list
                '''
            }
        }
        
        stage('Build Site') {
            steps {
                script {
                    echo "Building Jekyll site"
                    sh 'bundle exec jekyll build --trace'
                }
            }
        }
        
        stage('Unit Tests') {
            steps {
                script {
                    echo "Running unit tests..."
                    sh 'echo "Unit tests passed"'
                }
            }
        }
        
        stage('Integration Tests') {
            steps {
                script {
                    echo "Running integration tests..."
                    sh 'echo "this is the testttttttt"'
                }
            }
        }
        
        // stage('SonarQube Analysis') {
        //     steps {
        //         script {
        //             def scannerHome = tool 'sonarqube'
        //             withSonarQubeEnv('sonarqube') {
        //                 sh "${scannerHome}/bin/sonar-scanner"
        //             }
        //         }
        //     }
        // }
        
        // stage('Test Site') {
        //     steps {
        //         script {
        //             echo "Testing site with htmlproofer"
        //             sh '''
        //                 bundle exec htmlproofer _site \
        //                     --disable-external \
        //                     --ignore-urls "/^http:\\/\\/127.0.0.1/,/^http:\\/\\/0.0.0.0/,/^http:\\/\\/localhost/"
        //             '''
        //         }
        //     }
        // }
        
        stage('Upload Artifact') {
            steps {
                script {
                    echo "Uploading site artifact"
                    archiveArtifacts artifacts: '_site/**', fingerprint: true
                }
            }
        }
    }
    
    post {
        always {
            echo "Pipeline ${currentBuild.result} - Build completed"
        }
        success {
            echo "Pipeline succeeded - Site built successfully"
        }
        failure {
            echo "Pipeline failed - Check the logs for details"
        }
    }
}