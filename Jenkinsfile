pipeline {
  agent { label 'docker' }
  environment {
    IMAGE = 'vishnuvishalgn/hello-devops'
  }
  options { timestamps() }
  stages {
    stage('Checkout') { steps { checkout scm } }
    stage('Build Image') {
      steps {
        script { env.TAG = "${env.BUILD_NUMBER}" }
        sh 'docker build -t $IMAGE:$TAG -t $IMAGE:latest .'
      }
    }
    stage('Push Image') {
      steps {
        withCredentials([usernamePassword(credentialsId: 'dockerhub-creds',
                                          usernameVariable: 'USER',
                                          passwordVariable: 'PASS')]) {
          sh '''
            echo "$PASS" | docker login -u "$USER" --password-stdin
            docker push $IMAGE:$TAG
            docker push $IMAGE:latest
          '''
        }
      }
    }
  }
  post {
    success { echo "Pushed $IMAGE:$TAG and :latest" }
    always  { sh 'docker image prune -f || true'; cleanWs() }
  }
}
