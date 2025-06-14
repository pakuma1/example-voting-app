pipeline {
    agent any
    
    environment {
        DOCKERHUB_CREDENTIALS = credentials('dockerhub-demo') // stored in jenkins
        DOCKER_IMAGE = "pankajkumar159/${APP_NAME}:${BUILD_NUMBER}"
      //APP_NAME Select option "This build is parameterized" and select "string parameter"
    }
    stages{
        stage('Clone Repository') {
            steps {
                git branch: 'main', url: 'https://github.com/pakuma1/example-voting-app.git'
            }
        }
        stage('Build Docker Image') {
            steps {
                sh """
                  cd ${APP_NAME}
                  docker build -t ${DOCKER_IMAGE} .
                  """
            }
        }
        stage('DockerHub Login & Push') {
            steps {
                sh """
                echo ${DOCKERHUB_CREDENTIALS_PSW} | docker login -u ${DOCKERHUB_CREDENTIALS_USR} --password
                docker push ${DOCKER_IMAGE}
                """
            }
        }
        stage('Deploy to Kind Cluster') {
            steps {
                sh """
                  #Make sure kubectl is configured to point to kind cluster
                  #kubectl config use-context kind-netpol-demo
                  
                  #update image in deployment.yaml (optional)
                  #kubectl set image deployments/${APP_NAME} ${APP_NAME}=${DOCKER_IMAGE} --record || \
                  #kubectl apply -f k8s-specifications/${APP_NAME}-deployment.yaml
                  #kubectl apply -f k8s-specifications/${APP_NAME}-service.yaml
                  
                  #kubectl rollout status deployment/${APP_NAME}
                  """
            }
        }
    }
    post {
        success {
            echo "Deployment successful!"
        }
        failure {
            echo "Pipeline failed. Check logs."
        }
    }
}
