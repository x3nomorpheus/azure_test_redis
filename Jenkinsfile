  pipeline {

  environment {
    dockerimagename = "x3nomorpheus/redis"
    dockerImage = ""
  }

  agent any

  stages {

    stage('Build image') {
      steps{
        script {
          dockerImage = docker.build dockerimagename
        }
      }
    }

    stage('Pushing Image') {
      environment {
               registryCredential = 'dockerhub-credentials'
           }
      steps{
        script {
          docker.withRegistry( 'https://registry.hub.docker.com', registryCredential ) {
            dockerImage.push("latest")
          }
        }
      }
    }

    stage('Deploying Database container to Kubernetes') {
      steps {
         sh 'ssh -i /home/jenkins/.ssh/id_rsa -o "StrictHostKeyChecking no" kio@workstation kubectl --kubeconfig /home/kio/k8s/azurek8s delete deployment redis || true'
	 sh 'ssh -i /home/jenkins/.ssh/id_rsa -o "StrictHostKeyChecking no" kio@workstation kubectl --kubeconfig /home/kio/k8s/azurek8s apply -f /home/kio/k8s/kubernetes-apps/redis.yaml'
      }
    }
  }
}
