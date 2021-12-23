pipeline {

  environment {
    dockerimagename = "ilanm/hello-ilan:${GIT_COMMIT}"
    dockerImage = ""
  }
  tools { 
	maven 'maven384' 
	jdk 'jdk8'
  }

  agent any

  stages {
    stage('Checkout Source') {
        
      steps {
          git(
       url: 'https://github.com/meyrow/hello-ilan.git',
       branch: "main"
    )
        
        
      }
    }

	stage('Build maven') {
      steps {
        sh "mvn -Dmaven.test.failure.ignore=true clean package"
      }
    }

    stage('Build image') {
      steps{
          echo "start build image"
        script {
          def dockerHome = tool 'ilanDocker'
          env.PATH = "${dockerHome}/bin:${env.PATH}"
          dockerImage = docker.build dockerimagename
        }
        echo "end build image"
      }
    }

    stage('Pushing Image') {
      environment {
               registryCredential = 'dockerhublogin'
           }
      steps{
        script {
          docker.withRegistry( 'https://registry.hub.docker.com', registryCredential ) {
            dockerImage.push("latest")
          }
        }
      }
    }

    stage('Deploying App to Kubernetes') {
      steps {
        script {
          kubernetesDeploy(configs: "deployment.yml", kubeconfigId: "kubernetes")
        }
      }
    }
    stage('service') {
      steps {
        script {
          kubernetesDeploy(configs: "service.yml", kubeconfigId: "kubernetes")
        }
      }
    }
    stage('route') {
      steps {
        script {
          kubernetesDeploy(configs: "route.yml", kubeconfigId: "kubernetes")
        }
      }
    }

  }

}
