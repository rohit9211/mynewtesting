pipeline {
 agent {
  label "dev2"
 }

 environment {
  BRANCH_NAME = "*/release/stage2"
  GIT_TOOL = "Default"
  CREDENTIALSID = "bcd68c3f-31b8-424a-a3bb-4158c4c0b627"
  URL_NAME = "https://l02pigithub01.corp.local/AscenaNextGenTech/AnnFactory.git"
  RECIPIENTS = "rgaur@sapient.com"
 }

 options {
  timestamps()
 }

 stages {

  stage("Checkout") {
   steps {
    checkout([
     $class: 'GitSCM',
     branches: [
      [name: BRANCH_NAME]
     ],
     doGenerateSubmoduleConfigurations: false,
     extensions: [],
     gitTool: GIT_TOOL,
     userRemoteConfigs: [
      [
       credentialsId: CREDENTIALSID,
       url: URL_NAME
      ]
     ]
    ])
   }
  }
  stage('Install Dependencies') {
   steps {
    sh "cd ${WORKSPACE}"
    sh "PATH=$PATH:/usr/local/bin:/usr/local/node-v8.9.4-linux-x64/bin ; yarn install "
   }
  }
  stage('Test Cases') {
   steps {
    sh "cd ${WORKSPACE}"
    sh "PATH=$PATH:/usr/local/bin:/usr/local/node-v8.9.4-linux-x64/bin ; yarn test -u || :"
   }
  }
  stage('SonarQube Analysis') {
   steps {
    script {
     def scannerHome = tool 'Sonar 3.0.3';
     withSonarQubeEnv('Sonar') {
      sh "${scannerHome}/sonar-scanner";
     }
    }
   }
  }
  stage("SonarQube Quality Gate") {
   steps {
    script {
     def qualitygate = waitForQualityGate()
     if (qualitygate.status != "OK") {
      error "Pipeline aborted due to quality gate coverage failure: ${qualitygate.status}"
     }
    }
   }
  }
  stage("Code Compile") {
   steps {
    sh "cd ${WORKSPACE}"
    sh "PATH=$PATH:/usr/local/bin:/usr/local/node-v8.9.4-linux-x64/bin ; yarn run build "
   }
  }
  stage("Start Application") {
   steps {
    sh "cd ${WORKSPACE}"
    sh "PATH=$PATH:/usr/local/bin:/usr/local/node-v8.9.4-linux-x64/bin ; pm2 delete ecosystem.factory.config.js; pm2 start ecosystem.factory.config.js --env production"
   }
  }
 }
}
