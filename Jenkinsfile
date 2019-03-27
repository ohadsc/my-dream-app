pipeline {
  agent none
  stages {
    stage('Fetch dependencies') {
      agent {
        docker 'weboaks/node-karma-protractor-chrome'
      }
      steps {
        sh 'yarn'
        stash includes: 'node_modules/', name: 'node_modules'
      }
    }
    stage('Lint') {
      agent {
        docker 'weboaks/node-karma-protractor-chrome'
      }
      steps {
        unstash 'node_modules'
        sh 'yarn lint'
      }
    }
    stage('Unit Test') {
      agent {
        docker 'weboaks/node-karma-protractor-chrome'
      }
      steps {
        unstash 'node_modules'
        sh 'yarn test --watch=false'
      }
    }
    stage('E2E Test') {
      agent {
        docker 'weboaks/node-karma-protractor-chrome'
      }
      steps {
        unstash 'node_modules'
        sh 'yarn e2e'
      }
    }
    stage('Compile') {
      agent {
        docker 'weboaks/node-karma-protractor-chrome'
      }
      steps {
        unstash 'node_modules'
        sh 'yarn build --prod --aot'
        stash includes: 'dist/', name: 'dist'
      }
    }
    stage('Upload to S3 Folder') {
      when {
        branch 'master'
      }
      agent any
      steps {
        unstash 'dist'
        sh 'cd dist'
        withAWS(region:'us-east-2',credentials:'planny-aws-creds') {
                      s3Delete(bucket: 'planny-prod', path:'**/*')
                      s3Upload(bucket: 'planny-prod', workingDir:'dist/my-dream-app', includePathPattern:'**/*');
                    }
      }
    }
  }
}
