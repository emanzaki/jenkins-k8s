
pipeline {
  agent any
  environment {
    DOCKER_IMAGE = 'emanzaki/jenkins_app'
  }
  stages {
    stage('Run Unit Tests ...') {
      steps {
        sh ' mvn clean test'
      }
    }
    stage('Build App ...') {
      steps {
        sh 'mvn clean package -DskipTests'
      }
    }
    stage('Build Docker Image ...'){
      steps {
        sh "docker build -t $DOCKER_IMAGE:$BUILD_NUMBER ."
      }
    }
    stage('Push Docker Image ...') {
      steps {
        withCredentials([usernamePassword(credentialsId: 'docker' ,
        usernameVariable: 'DOCKER_USERNAME',
        passwordVariable: 'DOCKER_PASSWORD')]) {
          sh "docker login -u ${DOCKER_USERNAME} -p ${DOCKER_PASSWORD}"
          sh "docker push $DOCKER_IMAGE:$BUILD_NUMBER"
        }
      }
    }
    stage('Delete image local ...') {
      steps {
        sh "docker rmi $DOCKER_IMAGE:$BUILD_NUMBER"
      }
    }
    stage('Edit deployment.yaml ...') {
      steps {
        sh "sed -i 's|image: .* |image: $DOCKER_IMAGE:$BUILD_NUMBER|' deployment.yaml"
      }
    }
    stage('Deploy to K8s Cluster ...') {
      steps {
        sh "kubectl apply -f deployment.yaml"
      }
    }
  }
}