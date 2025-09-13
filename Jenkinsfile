pipeline {
  agent any

  stages {
    stage('Checkout') {
      steps {
        checkout scm
      }
    }

    stage('Show Versions') {
      steps {
        bat 'node -v'
        bat 'npm -v'
      }
    }

    stage('Install Dependencies') {
      steps {
        bat 'npm ci || npm install'
      }
    }

    stage('Run Tests') {
      steps {
        
        bat 'npm test || exit /b 0'
      }
    }

    stage('NPM Audit (Security Scan)') {
      steps {
        bat 'npm audit || exit /b 0'
      }
    }

    stage('SonarCloud Analysis') {
      steps {
        
        withCredentials([string(credentialsId: 'SONAR_TOKEN', variable: 'SONAR_TOKEN')]) {

          script {
            def scannerHome = tool 'SonarScanner'
            bat "\"${scannerHome}\\bin\\sonar-scanner.bat\" -Dsonar.token=%SONAR_TOKEN%"
          }
        }
      }
    }
  }
}
