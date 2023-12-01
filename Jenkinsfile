pipeline {
    agent any
    tools {
        maven 'Maven'
    }

    environment {
        PROJECT_ID = 'jenkins-296812'
        CLUSTER_NAME = 'k8s-cluster'
        LOCATION = 'us-central1-c'
        CREDENTIALS_ID = '571b31ee-87aa-4a07-bcca-2eebacc06f4d'  // Replace with your Kubernetes credential ID
        DOCKERHUB_USERNAME = 'aishu2000'
        DOCKERHUB_PASSWORD = 'aishu1122'
        IMAGE_NAME = 'helloworld'
        IMAGE_TAG = '1'
    }

    stages {
        stage('Scm Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build') {
            steps {
                sh 'mvn clean package'
            }
        }

        stage('Test') {
            steps {
                echo "Testing..."
                sh 'mvn test'
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    def myimage = docker.build("${DOCKERHUB_USERNAME}/${IMAGE_NAME}:${IMAGE_TAG}")
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                script {
                    echo "Push Docker Image"
                    sh "docker login -u ${DOCKERHUB_USERNAME} -p ${DOCKERHUB_PASSWORD}"
                    myimage.push("${IMAGE_TAG}")
                }
            }
        }

        stage('Deploy to K8s') {
            steps {
                echo "Deployment started ..."
                sh 'ls -ltr'
                sh 'pwd'
                sh "sed -i 's/tagversion/${IMAGE_TAG}/g' serviceLB.yaml"
                sh "sed -i 's/tagversion/${IMAGE_TAG}/g' deployment.yaml"
                
                // Assuming you have the Kubernetes plugin installed
                kubernetesDeploy(
                    kubeconfigId: env.CREDENTIALS_ID,
                    configs: 'serviceLB.yaml,deployment.yaml',
                    enableConfigSubstitution: true
                )
                
                echo "Deployment Finished ..."
            }
        }
    }
}
