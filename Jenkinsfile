pipeline {
    agent any
    environment {
        DOCKER_IMAGE = 'django-helloworld'
        DOCKER_CONTAINER = 'django-helloworld-container'
    }
    stages {
        stage('Checkout') {
            steps { git 'https://github.com/dasmaihar2017/django-helloworld.git' }
        }
        stage('Setup Python') {
            steps {
                sh '''
                python3 -m venv venv
                source venv/bin/activate
                pip install --upgrade pip
                pip install -r requirements.txt
                '''
            }
        }
        stage('Run Tests') {
            steps {
                sh '''
                source venv/bin/activate
                python3 manage.py test helloworld.tests
                '''
            }
        }
        stage('Build Docker Image') {
            steps {
                sh 'docker build -t $DOCKER_IMAGE .'
            }
        }
        stage('Run Docker Container') {
            steps {
                sh '''
                docker stop $DOCKER_CONTAINER || true
                docker rm $DOCKER_CONTAINER || true
                docker run -d -p 4000:8000 --name $DOCKER_CONTAINER $DOCKER_IMAGE
                '''
            }
        }
        stage('Smoke Test') {
            steps {
                sh 'sleep 5 && curl -f http://localhost:4000 || exit 1'
            }
        }
    }
    post {
        always {
            sh 'docker stop $DOCKER_CONTAINER || true && docker rm $DOCKER_CONTAINER || true'
        }
    }
}
