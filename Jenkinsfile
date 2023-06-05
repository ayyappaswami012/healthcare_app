pipeline {
   agent any

   environment {
     // You must set the following environment variables
     ORGANIZATION_NAME = "ayyappa"
     YOUR_DOCKERHUB_USERNAME = "ayyappaswami"
     SERVICE_NAME = "healthcare_app"
     REPOSITORY_TAG="${YOUR_DOCKERHUB_USERNAME}/${ORGANIZATION_NAME}-${SERVICE_NAME}:${BUILD_ID}"
   }

  stages {
      stage('Preparation') {
         steps {
            cleanWs()
            git credentialsId: 'GitHub', url: "https://github.com/${ORGANIZATION_NAME}/${SERVICE_NAME}"
         }
      }
      stage('Build') {
         steps {
            sh '''mvn clean package'''
         }
      }

      stage('Build and Push Image') {
         steps {
          sh '''
            docker image build -t ${REPOSITORY_TAG} .
            
            docker run -d -p 5000:5000 --name registry registry:2

            docker image tag ${REPOSITORY_TAG} localhost:5000/${ORGANIZATION_NAME}-${SERVICE_NAME}:${BUILD_ID}
            sleep 10
            docker push localhost:5000/${ORGANIZATION_NAME}-${SERVICE_NAME}:${BUILD_ID}
          '''
        }
           
      } 
    

      stage('Deploy to Cluster') {
          steps {
                   sh 'docker container run -d -e "SPRING_PROFILES_ACTIVE=local-microservice" --name webapp -p 8010:80 antrikshdevgan/k8s-fleetman-webapp-angular:release0-5'
          }
      }
   }
}
