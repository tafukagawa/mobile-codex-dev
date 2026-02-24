pipeline {
  agent { label 'macos-xcode' }

  options {
    timestamps()
    disableConcurrentBuilds()
    ansiColor('xterm')
  }

  environment {
    LC_ALL = 'en_US.UTF-8'
    LANG = 'en_US.UTF-8'
    BUNDLE_PATH = 'vendor/bundle'

    MATCH_PASSWORD = credentials('MATCH_PASSWORD')
    ASC_KEY_ID = credentials('ASC_KEY_ID')
    ASC_ISSUER_ID = credentials('ASC_ISSUER_ID')
    ASC_KEY_CONTENT_BASE64 = credentials('ASC_KEY_CONTENT_BASE64')
    AZURE_STORAGE_CONNECTION_STRING = credentials('AZURE_STORAGE_CONNECTION_STRING')
    SLACK_WEBHOOK_URL = credentials('SLACK_WEBHOOK_URL')
  }

  stages {
    stage('Checkout') {
      steps {
        checkout scm
      }
    }

    stage('Setup Ruby') {
      steps {
        sh 'bundle config set path ${BUNDLE_PATH}'
        sh 'bundle install --jobs 4 --retry 3'
      }
    }

    stage('Lint') {
      steps {
        sh 'bundle exec fastlane lint'
      }
    }

    stage('Test') {
      steps {
        sh 'bundle exec fastlane test'
      }
      post {
        always {
          junit allowEmptyResults: true, testResults: 'fastlane/test_output/report.junit'
        }
      }
    }

    stage('Build') {
      when {
        anyOf {
          branch 'develop'
          branch 'main'
          expression { env.BRANCH_NAME?.startsWith('release/') }
        }
      }
      steps {
        sh 'bundle exec fastlane build_app'
      }
    }

    stage('Upload Artifact to Azure') {
      when {
        anyOf {
          branch 'main'
          expression { env.BRANCH_NAME?.startsWith('release/') }
        }
      }
      steps {
        sh 'bundle exec fastlane upload_azure_artifact'
      }
    }

    stage('Deploy TestFlight') {
      when { branch 'main' }
      steps {
        sh 'bundle exec fastlane beta'
      }
    }

    stage('Deploy App Store') {
      when { expression { env.BRANCH_NAME?.startsWith('release/') } }
      steps {
        input message: 'App Store 申請を実行しますか？', ok: 'Deploy'
        sh 'bundle exec fastlane release'
      }
    }
  }

  post {
    success {
      sh 'bundle exec fastlane notify_success'
    }
    failure {
      sh 'bundle exec fastlane notify_failure'
    }
  }
}
