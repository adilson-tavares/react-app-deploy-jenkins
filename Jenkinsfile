pipeline {

  environment {
    dockerimagename = "tavarescruz/react-app"
    dockerImage = ""
  }

   agent { dockerfile true }

  stages {

    stage('Checkout Source') {
      steps {
        git branch: 'main', url: 'https://github.com/adilson-tavares/react-app-deploy-jenkins.git',
        credentialsId: 'github-credentials'
      }
    }

    stage('Build image') {
      steps{
        script {
        //   dockerImage = docker.build dockerimagename
        sh 'docker build -t tavarescruz/react-app .'
        }
      }
    }

    stage('Pushing Image') {
      environment {
               registryCredential = 'dokcerhub-credentials'
           }
      steps{
        script {
          docker.withRegistry( 'https://registry.hub.docker.com', registryCredential ) {
            dockerImage.push("latest")
          }
        }
      }
    }

    stage('Deploying React.js container to Kubernetes') {
      steps {
        script {
          kubernetesDeploy(configs: "deployment-app.yaml", "service-app.yaml")
        }
      }
    }

  }

}