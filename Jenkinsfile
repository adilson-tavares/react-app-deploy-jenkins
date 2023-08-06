pipeline {

  environment {
    dockerimagename = "tavarescruz/react-app"
    dockerImage = ""
  }

  agent {
    kubernetes {
      yaml '''
        apiVersion: v1
        kind: Pod
        spec:
          containers:
          - name: docker
            image: docker:latest
            command:
            - cat
            tty: true
            volumeMounts:
             - mountPath: /var/run/docker.sock
               name: docker-sock
          volumes:
          - name: docker-sock
            hostPath:
              path: /var/run/docker.sock    
        '''
    }
  }

    // agent {
    //     docker { image 'node:18.16.0-alpine' }
    // }

  // environment {
  //   DOCKERHUB_CREDENTIALS = credentials('dokcerhub-credentials')
  // }

  stages {
    stage('Checkout Source') {
      steps {
        git branch: 'main', url: 'https://github.com/adilson-tavares/react-app-deploy-jenkins.git',
        credentialsId: 'github-credentials'
      }
    }
    stage('Build image') {
      steps{
        // script {
        //   dockerImage = docker.build dockerimagename
        // }
        container('docker') {
           sh 'docker build -t tavarescruz/react-app:latest .'
        }
      }
    }
    stage('Pushing Image') {

      environment {
          registryCredential = credentials('docker-hub-credential')
      }
      steps {
        sh 'echo $registryCredential_PSW | docker login -u $registryCredential_USR --password-stdin'
      }
      // environment {
      //          registryCredential = 'docker-hub-credential'
      //      }
      // steps{
      //   script {
      //     docker.withRegistry( 'https://registry.hub.docker.com', registryCredential ) {
      //       dockerImage.push("latest")
      //     }
      //   }
      // }
    }
    stage('Push Image to DockerHub') {
      steps {
        container('docker') {
          sh 'docker push tavarescruz/react-app:latest'
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