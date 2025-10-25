node {
  stage('SCM') {
    checkout scm
  }
  stage('SonarQube analysis') {
    def scannerHome = tool 'sonarqube'; // must match the name of an actual scanner installation directory on your Jenkins build agent
    withSonarQubeEnv('sonarqube') { // If you have configured more than one global server connection, you can specify its name as configured in Jenkins
      sh "${scannerHome}/bin/sonar-scanner"
    }
  }

}
