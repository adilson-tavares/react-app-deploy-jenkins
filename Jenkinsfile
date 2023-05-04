pipeline {

  // environment {
  //   dockerimagename = "tavarescruz/react-app"
  //   dockerImage = ""
  // }

  agent any

  options {
    buildDiscarder(logRotator(numToKeepStr: '5'))
  }
  environment {
    DOCKERHUB_CREDENTIALS = credentials('dokcerhub-credentials')
  }

  stages {

    stage('Checkout Source') {
      steps {
        git branch: 'main', url: 'https://github.com/adilson-tavares/react-app-deploy-jenkins.git',
        credentialsId: 'github-credentials'
      }
    }

    stages {
      stage('Build') {
        steps {
          sh 'docker build -t lloydmatereke/jenkins-docker-hub .'
        }
      }
      stage('Login') {
        steps {
          sh 'echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin'
        }
      }
      stage('Push') {
        steps {
          sh 'docker push lloydmatereke/jenkins-docker-hub'
        }
      }
    }

    // stage('Build image') {
    //   steps{
    //     script {
    //     //   dockerImage = docker.build dockerimagename
    //     sh 'docker build -t tavarescruz/react-app .'
    //     }
    //   }
    // }

    // stage('Pushing Image') {
    //   environment {
    //            registryCredential = 'dokcerhub-credentials'
    //        }
    //   steps{
    //     script {
    //       docker.withRegistry( 'https://registry.hub.docker.com', registryCredential ) {
    //         dockerImage.push("latest")
    //       }
    //     }
    //   }
    // }

    stage('Deploying React.js container to Kubernetes') {
      steps {
        script {
          kubernetesDeploy(configs: "deployment-app.yaml", "service-app.yaml")
        }
      }
    }
    post {
      always {
        sh 'docker logout'
      }
    }

  }

}