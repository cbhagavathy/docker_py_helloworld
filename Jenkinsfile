pipeline {
    agent any

    environment {
        IMAGE_NAME = "flask-hello"
        CONTAINER_NAME = "flask_hello_container"
        GIT_REPO = "https://github.com/cbhagavathy/docker_py_helloworld.git"
    }

    stages {
        stage('Clone Repo') {
            steps {
                git credentialsId: 'your-git-credentials-id', url: "${GIT_REPO}"
            }
        }

        stage('Build Docker Image') {
            steps {
                sh "docker build -t ${IMAGE_NAME} ."
            }
        }

        stage('Push to Docker Hub (optional)') {
            when {
                expression { return env.PUSH_TO_HUB == 'true' }
            }
            steps {
                sh "docker tag ${IMAGE_NAME} cbhagavathy/${IMAGE_NAME}"
                withCredentials([usernamePassword(credentialsId: 'dockerhub-creds', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                    sh "echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin"
                    sh "docker push cbhagavathy/${IMAGE_NAME}"
                }
            }
        }

        stage('Run Ansible for Deployment') {
            steps {
                sh """
                cd ansible
                ansible-playbook -i inventory deploy.yml
                """
            }
        }
    }
}

