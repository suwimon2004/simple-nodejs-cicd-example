pipeline {

  agent {
    kubernetes {
      yaml '''
apiVersion: v1
kind: Pod
spec:
  containers:
  - name: my-builder
    image: node:20-alpine
    command:
    - cat
    tty: true
'''
    }
  }

  stages {

    stage('Check Environment') {
      steps {
        container('my-builder') {
          sh 'node --version'
          sh 'npm --version'
        }
      }
    }

    stage('Install Dependencies') {
      steps {
        container('my-builder') {
          sh 'npm ci'
        }
      }
    }

    stage('Run Tests') {
      steps {
        container('my-builder') {
          sh 'npm test'
        }
      }
    }

    stage('Deploy') {
      steps {
        container('my-builder') {
          withCredentials([string(credentialsId: 'vercel-token', variable: 'VERCEL_TOKEN')]) {
            sh 'npx vercel --prod --token=$VERCEL_TOKEN --yes --name simple-nodejs-api'
          }
        }
      }
    }

  }
}
