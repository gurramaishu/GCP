pipeline {
    agent any
    tools {
        maven 'Maven'
    }
 
    environment {
        PROJECT_ID = 'diesel-harmony-406010'
        CLUSTER_NAME = 'k8scluster-1'
        LOCATION = 'us-central1-a'
        CREDENTIALS_ID = 'kubernetes'
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
                    // Define myimage as a global variable
                    myimage = docker.build("${DOCKERHUB_USERNAME}/${IMAGE_NAME}:${IMAGE_TAG}")
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

        script {
            // Update 'kubectl' to the name you configured in Jenkins (e.g., 'kubectl').
            def kubectl = tool name: 'kubectl', type: 'kubectl'
            sh "${kubectl} apply -f serviceLB.yaml"
            sh "${kubectl} apply -f deployment.yaml"
        }

        echo "Deployment Finished ..."
    }
  }
 }
}
 
