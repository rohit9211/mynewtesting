pipeline {
  agent {
    node {
      label 'dev2'
    }
    
  }
  stages {
    stage('checkout') {
      steps {
        git(url: 'https://l02pigithub01.corp.local/AscenaNextGenTech/AnnFactory.git', branch: '*/release/stage2', changelog: true, credentialsId: '	bcd68c3f-31b8-424a-a3bb-4158c4c0b627', poll: true)
      }
    }
  }
}