pipeline {
  agent any
  stages {
    stage('CI') {
      steps {
        git(url: 'ssh://git@stash.amer.thermo.com:7999/icma/icma-android.git', branch: '*/master', poll: true, changelog: true)
      }
    }
    stage('Unit Test') {
      parallel {
        stage('Unit Test Dev') {
          steps {
            sh './gradlew -i devLintOptions'
            sh './gradlew -i assembleDevDebug'
            sh '''
./gradlew -i testDevDebugUnitTest'''
          }
        }
        stage('Unit Test Test') {
          steps {
            sh './gradlew -i envTestLintOptions'
            sh './gradlew -i assembleEnvTestDebug'
            sh '''
./gradlew -i testEnvTestDebugUnitTest'''
          }
        }
      }
    }
    stage('Update Version and create tag') {
      steps {
        sh '''echo "Pointing to master"
git checkout master'''
        sh './gradlew -i updateVersion'
        sh '''echo "Git commit"
git commit app/version.properties -m "updated version.properties"
'''
        sh '''echo "Git push"
git push'''
        sh '''#!/bin/bash
echo "Creating a tag"
source app/version.properties
echo $TAG_NAME
git tag $TAG_NAME
git push origin $TAG_NAME'''
      }
    }
    stage('Release') {
      parallel {
        stage('Release Dev') {
          steps {
            sh '''echo "Building APK file"
echo $TAG_NAME
./gradlew app:assembleDevDebug -i'''
            sh '''echo "Uploading Build To Crashlytics"
./gradlew crashlyticsUploadDistributionDevDebug -i'''
          }
        }
        stage('Release Test') {
          steps {
            sh '''echo "Building APK file"
echo $TAG_NAME
./gradlew app:assembleEnvtestDebug -i'''
            sh '''echo "Uploading Build To Crashlytics"
./gradlew crashlyticsUploadDistributionEnvtestDebug -i'''
          }
        }
      }
    }
  }
}