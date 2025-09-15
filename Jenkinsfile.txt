// If Jenkins runs on macOS/Linux, replace all `bat '...'` with `sh '...'` and change `|| exit /b 0` to `|| true`.
pipeline {
  agent any
  triggers { pollSCM('H/5 * * * *') }

  environment {
    REPO_URL = 'https://github.com/LalithaDeepthiTalasila/8.2CDevSecOps.git'
  }

  stages {
    stage('Checkout') {
      steps { git branch: 'main', url: "${env.REPO_URL}" }
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
  }

  post {
    always { echo 'Pipeline finished.' }
  }
}