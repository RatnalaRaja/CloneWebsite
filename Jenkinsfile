



pipeline {
    agent {
        docker {
            image 'docker:24.0.7'
            #args '-v /var/run/docker.sock:/var/run/docker.sock'
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
        stage('Create Dockerfile') {
            steps {
                sh '''
                    echo "Generating Dockerfile..."
                    cat <<EOF > Dockerfile
                    FROM node:18
                    WORKDIR /app
                    RUN npm install -g serve
                    COPY index.html .
                    EXPOSE 3000
                    CMD ["serve", "-l", "3000", "."]
                    EOF

                    echo "<h1>Hello from Jenkins container on port 3000</h1>" > index.html
                '''
            }
        }

        stage('Build Image') {
            steps {
                sh 'docker build -t $IMAGE_NAME:$IMAGE_TAG .'
            }
        }

        stage('Run Container') {
            steps {
                // Stop existing container if running
                sh '''
                    docker stop $CONTAINER_NAME || true
                    docker rm $CONTAINER_NAME || true

                    docker run -d --name $CONTAINER_NAME -p $HOST_PORT:$CONTAINER_PORT $IMAGE_NAME:$IMAGE_TAG
                '''
            }
        }

        stage('Confirm Running') {
            steps {
                sh 'docker ps | grep $CONTAINER_NAME'
            }
        }
    }
}
