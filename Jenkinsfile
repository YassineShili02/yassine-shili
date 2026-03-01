pipeline {
    agent any
    environment {
        DOCKER_IMAGE = "yassineshili/yassine-app"
        DOCKER_TAG = "1.0"
        KUBECONFIG = "/var/lib/jenkins/.kube/config"
    }
    stages {
        stage('Git Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/YassineShili02/yassine-shili'
            }
        }
        stage('Build') {
            steps {
                sh 'mvn clean package -DskipTests'
            }
        }
        stage('Docker Build & Push') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub-credentials',
                    usernameVariable: 'DOCKER_USER',
                    passwordVariable: 'DOCKER_PASS'
                )]) {
                    sh '''
                        docker build -t $DOCKER_IMAGE:$DOCKER_TAG .
                        echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin
                        docker push $DOCKER_IMAGE:$DOCKER_TAG
                    '''
                }
            }
        }
        stage('Kubernetes Deploy') {
            steps {
                sh 'kubectl apply -f mysql-deployment.yaml -n projet'
                sh 'sleep 15'
                sh 'kubectl apply -f spring-deployment.yaml -n projet'
            }
        }
        stage('Deploy MySQL & Spring Boot on K8s') {
            steps {
                sh 'kubectl get pods -n projet'
                sh 'kubectl get svc -n projet'
            }
        }
    }
    post {
        success { echo 'Deploiement reussi!' }
        failure { echo 'Echec du pipeline.' }
    }
}
