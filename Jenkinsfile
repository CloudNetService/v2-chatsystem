pipeline {
  agent any
  tools {
        maven 'Maven3'
        jdk 'Java8'
  }
  options {
    buildDiscarder logRotator(numToKeepStr: '10')
  }
  stages {
    stage('Clean') {
      steps {
        sh 'mvn clean'
      }
    }
    stage('Compile') {
      steps {
        sh 'mvn compile'
      }
    }
    stage('Test') {
      steps {
        sh 'mvn test'
        junit testResults: '**/target/surefire-reports/*.xml', allowEmptyResults: true
      }
    }
    stage('Package') {
      steps {
        sh 'mvn package'
      }
    }
    stage('Release ZIP') {
      steps {
        sh '''mkdir -p temp;
        cp chat-plugin/target/*.jar temp/;'''
        zip archive: true, dir: 'temp', glob: '', zipFile: 'ChatSystem.zip'
        sh 'rm -r temp/';
      }
    }
    stage('Archive') {
      steps {
        archiveArtifacts allowEmptyArchive: true, artifacts: '**/target/Chat*.jar', fingerprint: true, onlyIfSuccessful: true
      }
    }
  }
  post {
    always {
      withCredentials([string(credentialsId: 'cloudnet-discord-ci-webhook', variable: 'url')]) {
        discordSend description: 'New build for ChatSystem!', footer: 'New build!', link: env.BUILD_URL, successful: currentBuild.resultIsBetterOrEqualTo('SUCCESS'), title: JOB_NAME, webhookURL: url
      }
    }
  }
}