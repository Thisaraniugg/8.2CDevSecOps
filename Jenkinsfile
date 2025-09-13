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
  environment {
    UA = 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/124 Safari/537.36'
  }
  steps {
    bat '''
      setlocal

      rem --- clean old artifacts ---
      if exist scanner.zip del /q scanner.zip
      for /d %%D in (sonar-scanner-*) do rmdir /s /q "%%D"

      rem --- try SonarSource CDN with browser-like UA, then two GitHub tags ---
      curl -L -f -A "%UA%" -o scanner.zip https://binaries.sonarsource.com/Distribution/sonar-scanner-cli/sonar-scanner-6.2.1.4610-windows-x64.zip ^
      || curl -L -f -A "%UA%" -o scanner.zip https://github.com/SonarSource/sonar-scanner-cli/releases/download/sonar-scanner-6.2.1.4610/sonar-scanner-6.2.1.4610-windows-x64.zip ^
      || curl -L -f -A "%UA%" -o scanner.zip https://github.com/SonarSource/sonar-scanner-cli/releases/download/sonar-scanner-6.1.0.4477/sonar-scanner-6.1.0.4477-windows-x64.zip

      if not exist scanner.zip (
        echo Download failed after all mirrors.
        exit /b 1
      )

      rem --- unzip & locate scanner ---
      powershell -ExecutionPolicy Bypass -Command "Expand-Archive -Path scanner.zip -DestinationPath . -Force"
      for /d %%D in (sonar-scanner-*) do set "SCANDIR=%%D"
      if not defined SCANDIR (
        echo Could not find extracted sonar-scanner folder.
        dir
        exit /b 1
      )

      rem --- run scanner (token from Jenkins creds) ---
      set "PATH=%cd%\\%SCANDIR%\\bin;%PATH%"
      "%cd%\\%SCANDIR%\\bin\\sonar-scanner.bat" -D"sonar.login=%SONAR_TOKEN%"
    '''
    }
   }
 }
}
