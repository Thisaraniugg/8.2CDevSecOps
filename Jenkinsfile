pipeline {
  agent any
  environment {
  SONAR_TOKEN = credentials('SONAR_TOKEN')
}
  stages {
    stage('Checkout') {
      steps {
        git branch: 'main', url: 'https://github.com/Thisaraniugg/8.2CDevSecOps.git'
      }
    }
    stage('Show Versions') {
      steps {
        bat 'node -v'
        bat 'npm -v'
      }
    }
    stage('Install Dependencies') {
      steps { bat 'npm install' }
    }
    stage('Run Tests') {
      steps { bat 'npm test || exit /b 0' }
    }
    stage('Generate Coverage Report') {
      steps { bat 'npm run coverage || exit /b 0' }
    }
    stage('NPM Audit (Security Scan)') {
      steps { bat 'npm audit || exit /b 0' }
    }
     stage('SonarCloud Analysis') {
      steps {
        withCredentials([string(credentialsId: 'SONAR_TOKEN', variable: 'SONAR_TOKEN')]) {
          bat '''
            call npx --yes sonar-scanner ^
              -Dsonar.host.url=https://sonarcloud.io ^
              -Dsonar.organization=thisaraniugg ^
              -Dsonar.projectKey=thisaraniugg_thisaraniugg-8-2cdevsecops ^
              -Dsonar.token=%SONAR_TOKEN%
          '''
        }
      }
    }
 }
}
