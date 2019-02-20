pipeline {
  agent any
  stages {
    stage('CI') {
      steps {
        git(url: 'ssh://git@stash.amer.thermo.com:7999/icma/icma-android.git', branch: '*/master', poll: true, changelog: true)
      }
    }
  }
}