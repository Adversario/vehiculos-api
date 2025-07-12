pipeline {
  agent any
  tools { maven 'M3' }           // usa la Maven 3.9 definida en Jenkins

  environment {
    IMAGE = 'imagen_vehiculos'
    CONTAINER = 'contenedor_sucursal'
    PORTS = '9090:8080'
  }

  stages {
    stage('Checkout') {
      steps { checkout scm }
    }

    stage('Build') {
      steps {
        sh 'mvn -B clean package -DskipTests'
      }
    }

    stage('Docker Build') {
      steps {
        sh "docker build -t $IMAGE ."
      }
    }

    stage('Deploy') {
      steps {
        sh "docker stop $CONTAINER || true"
        sh "docker rm $CONTAINER || true"
        sh "docker run -d -p $PORTS --name $CONTAINER $IMAGE"
      }
    }
  }

  triggers { githubPush() }
}

