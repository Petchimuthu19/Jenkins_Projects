pipeline {
  agent any

  environment {
    DOCKER_IMAGE = "petchimuthu1995/cred_app"
    REGISTRY_CRED = "dockerhub-creds"
    KUBECONFIG = "kubeconfig-credentials"
  }

  stages {

    stage('Checkout Code') {
      steps {
        git branch: 'main',
            url: 'https://github.com/Petchimuthu19/Jenkins_Projects.git',
            credentialsId: 'github-creds'
      }
    }

    stage('Build Docker Image') {
      steps {
        script {
          dockerImage = docker.build("${DOCKER_IMAGE}:${env.BUILD_NUMBER}")
        }
      }
    }

    stage('Push Docker Image') {
      steps {
        script {
          docker.withRegistry('https://index.docker.io/v1/', REGISTRY_CRED) {
            dockerImage.push("${env.BUILD_NUMBER}")
            dockerImage.push("latest")
          }
        }
      }
    }

    stage('Deploy to Kubernetes') {
      steps {
        withCredentials([file(credentialsId: "${KUBECONFIG}", variable: 'KUBECONFIG_FILE')]) {
          sh '''
          export KUBECONFIG=$KUBECONFIG_FILE
          kubectl apply -f deployment.yaml
          kubectl apply -f service.yaml
          '''
        }
      }
    }
  }
}
