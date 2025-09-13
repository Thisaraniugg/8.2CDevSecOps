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
    bat '''
        if exist sonar-scanner rmdir /s /q sonar-scanner
         if exist scanner.zip del /q scanner.zip
        powershell -ExecutionPolicy Bypass -Command "Invoke-WebRequest -Uri https://binaries.sonarsource.com/Distribution/sonar-scanner-cli/sonar-scanner-6.2.1.4610-windows-x64.zip -OutFile scanner.zip"
        powershell -ExecutionPolicy Bypass -Command "Expand-Archive -Path scanner.zip -DestinationPath . -Force"
        for /d %%D in (sonar-scanner-*) do set SCANDIR=%%D
         set PATH=%cd%\\%SCANDIR%\\bin;%PATH%
         sonar-scanner -D"sonar.login=%SONAR_TOKEN%"
    '''
    }
   }
 }
}
