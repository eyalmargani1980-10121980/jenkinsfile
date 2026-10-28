pipeline {
    agent any

    parameters {
        string(name: 'NAME', defaultValue: 'Daniel', description: 'Enter your name')
        choice(name: 'ENVIRONMENT', choices: ['dev', 'stage', 'prod'], description: 'Select an environment')
        booleanParam(name: 'RUN_TESTS', defaultValue: true, description: 'Run tests?')
    }

    environment {
        IMAGE_NAME = 'eyalmargani1980/flask-app'
    }

    stages {
        stage('Greeting & Env Setup') {
            steps {
                echo "Hello ${params.NAME}!"
                echo "Selected environment: ${params.ENVIRONMENT}"
            }
        }

        stage('Code Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/eyalmargani1980-10121980/jenkinsfile.git'
            }
        }

        stage('Tests') {
            when {
                expression { return params.RUN_TESTS }
            }
            steps {
                echo 'Running application tests...'
            }
        }

        stage('Docker Build+Tag') {
            steps {
                sh "docker build -t ${IMAGE_NAME}:latest ."
            }
        }

        stage('Docker Push') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub-credentials', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                    sh '''
                    echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin
                    docker push ${IMAGE_NAME}:latest
                    '''
                }
            }
        }

        stage('Docker Run') {
            steps {
                sh '''
                docker stop flask-app || true
                docker rm flask-app || true
                docker run -d -p 5000:5000 --name flask-app ${IMAGE_NAME}:latest
                '''
            }
        }
    }
}
