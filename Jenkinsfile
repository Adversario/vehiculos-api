pipeline {
    agent any

    environment {
        IMAGE     = 'vehiculos-api:latest'
        CONTAINER = 'contenedor_sucursal'
        NETWORK   = 'vehiculos-net'
        PORTS     = '9090:8080'
    }

    stages {

        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build') {
            steps {
                sh 'mvn -B clean package -DskipTests'
            }
        }

        stage('Docker Build') {
            steps {
                sh """
                docker build -t $IMAGE .
                """
            }
        }

        stage('Deploy') {
            steps {
                script {
                    sh """
                    # -- detener / borrar contenedor previo (si existe)
                    docker stop  $CONTAINER  || true
                    docker rm    $CONTAINER  || true

                    # -- crea la red sólo si no existe
                    docker network inspect $NETWORK >/dev/null 2>&1 || \
                      docker network create $NETWORK

                    # -- conecta MySQL a la red (ignora error si ya estaba)
                    docker network connect $NETWORK vehiculos-mysql 2>/dev/null || true

                    # -- contenedor de la API
                    docker run -d --network $NETWORK -p $PORTS \
                               --name $CONTAINER $IMAGE
                    """
                }
            }
        }
    }
}
