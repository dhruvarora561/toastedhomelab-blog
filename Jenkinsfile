pipeline {
    agent any
    
    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }
        
        stage('Unit Tests') {
            steps {
                script {
                    echo "Running unit tests..."
                    // Add your unit test commands here
                    // sh 'npm test' or 'pytest' or 'mvn test'
                    sh 'echo "Unit tests passed"'
                }
            }
        }
        
        stage('Integration Tests') {
            steps {
                script {
                    error ("Running integration tests...")
                    // Add your integration test commands here  
                    // sh 'npm run integration-test' or other commands
                    sh 'echo "Integration tests passed"'
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
    }
    
    post {
        always {
            echo "Pipeline ${currentBuild.result} - Status checks completed"
        }
    }
}