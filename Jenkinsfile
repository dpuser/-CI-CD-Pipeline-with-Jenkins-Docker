
// ### 2. Jenkinsfile

// ```groovy
pipeline {
    agent any

    environment {
        // Update these values to match your Docker registry and credentials
        REGISTRY = "3122022"
        IMAGE_NAME = "sample-app"
        IMAGE_TAG = "latest"
        DOCKER_CREDENTIALS_ID = "dockerhub-credentials"
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    dockerImage = docker.build("${REGISTRY}/${IMAGE_NAME}:${IMAGE_TAG}", "./app")
                }
            }
        }

        stage('Test') {
            steps {
                echo 'Running tests inside Docker container...'
                sh 'docker run --rm ${REGISTRY}/${IMAGE_NAME}:${IMAGE_TAG} python -m unittest discover -s test'
            }
        }

        stage('Push Docker Image') {
            steps {
                script {
                    docker.withRegistry('https://index.docker.io/v1/', DOCKER_CREDENTIALS_ID) {
                        dockerImage.push()
                    }
                }
            }
        }

        // stage('Deploy to Kubernetes') {
        //     steps {
        //         echo 'Deploying to Kubernetes using Ansible...'
        //         sh 'ansible-playbook -i ansible/inventory.ini ansible/deploy.yml'
        //     }
        // }
        stage('Apply Kubernetes files') {
            steps {
            //  sh label: '', script: '''scp *.yml admin@172.28.96.203:/C:\Program Files\Jenkins
                sh label: '', script: '''scp *.yml admin@172.28.96.203:/var/lib/Jenkins
                                    ssh jenkins@172.28.96.203 kubectl apply -f  /k8s/deployment.yaml
                                    ssh jenkins@172.28.96.203 kubectl apply -f service.yml'''
           
        }
     }
    }

    post {
        always {
            cleanWs()
        }
    }
}
