pipeline {
  agent any
  stages {
    stage('Lint HTML') {
      steps {
        sh 'tidy -q -e *.html'
      }
    }

    stage('Cloning Git') {
      steps {
        git 'https://github.com/mimiobi/udacity-capstone-projectv.git'
      }
    }

    stage('Building image') {
      steps {
        script {
          sh 'docker build --tag=mimiobi/udacity-capstone .'
        }

      }
    }

    stage('Deploy Image') {
      steps {
        script {
          withDockerRegistry([ credentialsId: "docker-hub", url: "" ]) {
            sh 'docker push mimiobi/udacity-capstone'
          }
        }

      }
    }

    stage('Apply blue green deployment ') {
      steps {
        script {
          sh 'kubectl apply -f deployment/blue-green-webapp-deploy.yaml'
        }

      }
    }

    stage('Verify status of blue green deployment ') {
      steps {
        script {
          sh 'kubectl rollout status deployment/blue-green-deployment'
          sh 'kubectl rollout history deployment/blue-green-deployment'
        }

      }
    }

  }
  environment {
    registry = 'mimiobi/udacity-capstone'
    registryCredential = 'docker-hub'
  }
}
