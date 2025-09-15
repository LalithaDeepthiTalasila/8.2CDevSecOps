// If her Jenkins agent is macOS/Linux, replace bat '...' with sh '...' and '|| exit /b 0' with '|| true'.
pipeline {
  agent any
  triggers { pollSCM('H/5 * * * *') }

  environment {
    REPO_URL     = 'https://github.com/LalithaDeepthiTalasila/8.2CDevSecOps.git'
    NOTIFY_EMAIL = 'lalithadeepthi789@gmail.com'
  }

  stages {
    stage('Checkout') { steps { git branch: 'main', url: "${env.REPO_URL}" } }

    stage('Install Dependencies') { steps { bat 'npm install' } }

    stage('Run Tests') {
      steps {
        bat 'npm test || exit /b 0'
        script {
          emailext(
            to: "${env.NOTIFY_EMAIL}",
            subject: "SIT753-8.1C • Test stage • ${env.JOB_NAME} #${env.BUILD_NUMBER}",
            body: """Stage: Run Tests
Job: ${env.JOB_NAME}
Build: ${env.BUILD_URL}
Result so far: ${currentBuild.currentResult}""",
            attachLog: true,
            mimeType: 'text/plain'
          )
        }
      }
    }

    stage('Generate Coverage Report') { steps { bat 'npm run coverage || exit /b 0' } }

    stage('NPM Audit (Security Scan)') {
      steps {
        bat 'npm audit || exit /b 0'
        script {
          emailext(
            to: "${env.NOTIFY_EMAIL}",
            subject: "SIT753-8.1C • Security scan • ${env.JOB_NAME} #${env.BUILD_NUMBER}",
            body: """Stage: NPM Audit (Security Scan)
Job: ${env.JOB_NAME}
Build: ${env.BUILD_URL}
Result so far: ${currentBuild.currentResult}""",
            attachLog: true,
            mimeType: 'text/plain'
          )
        }
      }
    }
  }

  post {
    always { echo 'Pipeline finished (emails sent after Test and Security stages).' }
  }
}