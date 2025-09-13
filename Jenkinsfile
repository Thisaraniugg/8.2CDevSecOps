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
            setlocal EnableDelayedExpansion

      rem --- clean old artifacts ---
      if exist scanner.zip del /q scanner.zip
      for /d %%D in (sonar-scanner-*) do rmdir /s /q "%%D"

      rem --- primary download (force TLS1.2) ---
      powershell -ExecutionPolicy Bypass -Command ^
        "[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12; ^
         try { Invoke-WebRequest -UseBasicParsing -Uri 'https://binaries.sonarsource.com/Distribution/sonar-scanner-cli/sonar-scanner-6.2.1.4610-windows-x64.zip' -OutFile 'scanner.zip' } ^
         catch { exit 1 }"

      rem --- fallback: GitHub mirror if primary failed ---
      if errorlevel 1 (
        echo Primary download failed, trying GitHub mirror...
        powershell -ExecutionPolicy Bypass -Command ^
          "[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12; ^
           Invoke-WebRequest -UseBasicParsing -Uri 'https://github.com/SonarSource/sonar-scanner-cli/releases/download/v6.2.1.4610/sonar-scanner-6.2.1.4610-windows-x64.zip' -OutFile 'scanner.zip'"
      )

      rem --- unzip & locate scanner ---
      powershell -ExecutionPolicy Bypass -Command "Expand-Archive -Path scanner.zip -DestinationPath . -Force"
      for /d %%D in (sonar-scanner-*) do set "SCANDIR=%%D"
      set "PATH=%cd%\\!SCANDIR!\\bin;%PATH%"

      rem --- run scanner with Jenkins credential ---
      "%cd%\\!SCANDIR!\\bin\\sonar-scanner.bat" -D"sonar.login=%SONAR_TOKEN%"
    '''
    }
   }
 }
}
