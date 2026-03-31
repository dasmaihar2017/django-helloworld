pipeline {
    agent any

    environment {
        DOCKER_IMAGE = 'django-helloworld'
        DOCKER_CONTAINER = 'django-helloworld-container'
    }

    stages {
        stage('Checkout') {
            steps {
                git 'https://github.com/dasmaihar2017/django-helloworld.git'
            }
        }

        stage('Setup Python') {
            steps {
                sh '''
                python3 -m venv venv
                ./venv/bin/pip install --upgrade pip
                ./venv/bin/pip install -r requirements.txt
                '''
            }
        }

        stage('Run Tests') {
            steps {
                sh './venv/bin/python3 manage.py test helloworld.tests'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t $DOCKER_IMAGE .'
            }
        }

        stage('Deploy Container') {
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
                sh 'sleep 5 && curl -f http://localhost:4000'
            }
        }
    }

    post {
        failure {
            sh 'docker stop $DOCKER_CONTAINER || true && docker rm $DOCKER_CONTAINER || true'
        }
    }
}
