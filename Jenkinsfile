pipeline {
    agent {
        docker {
            image 'node:18' // Node.js + bash + npm
            args '-v /var/run/docker.sock:/var/run/docker.sock'
        }
    }

    environment {
        IMAGE_NAME = 'demo-local'
        IMAGE_TAG = 'v1'
        CONTAINER_NAME = 'demo-container'
        HOST_PORT = '3000'
        CONTAINER_PORT = '3000'
    }

    stages {
        stage('Clone Repo') {
            steps {
                // This clones the repo into workspace — required before using git commands
                checkout scm

                // Optional: check .git presence
                sh 'ls -a'
            }
        }

        stage('Generate Dockerfile') {
            steps {
                sh '''
                    echo "Generating Dockerfile..."
                    cat <<EOF > Dockerfile
FROM node:18
WORKDIR /app
RUN npm install -g serve
COPY . .
EXPOSE 3000
CMD ["serve", "-l", "3000", "."]
EOF
                '''
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t $IMAGE_NAME:$IMAGE_TAG .'
            }
        }

        stage('Run Container') {
            steps {
                sh '''
                    docker stop $CONTAINER_NAME || true
                    docker rm $CONTAINER_NAME || true
                    docker run -d --name $CONTAINER_NAME -p $HOST_PORT:$CONTAINER_PORT $IMAGE_NAME:$IMAGE_TAG
                '''
            }
        }

        stage('Check Running Container') {
            steps {
                sh 'docker ps | grep $CONTAINER_NAME'
            }
        }
    }
}
