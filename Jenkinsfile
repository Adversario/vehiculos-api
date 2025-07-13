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
        script {
            sh """
            docker stop $CONTAINER || true
            docker rm   $CONTAINER || true

            # (re)create network only if it doesn't exist
            docker network inspect vehiculos-net >/dev/null 2>&1 || \
              docker network create vehiculos-net

            # conecta (o arranca) MySQL en la red
            docker network connect vehiculos-net vehiculos-mysql 2>/dev/null || true

            # contenedor de la API en la misma red
            docker run -d \
              --network vehiculos-net \
              -p 9090:8080 \
              --name $CONTAINER \
              $IMAGE
            """
        }
    }
}


  triggers { githubPush() }
}
