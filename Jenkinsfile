pipeline {
    agent any
    
    environment { 
      DOCKERHUB_UN = 'mahaabuseif'
    }
    
    stages {
        stage('Build & Test') {
            steps {
              sh '''
              docker image build -t $DOCKERHUB_UN/image:${GIT_COMMIT} ./app
              '''
            }
        }
        
        stage('Delivery') {
            steps {
                   sh '''
                    docker login -u mahaabuseif -p OBSMaSaH@774433
                    docker push $DOCKERHUB_UN/image:${GIT_COMMIT}
                   '''
            }
       }
        
         stage('Deploy') {
            steps {
              sh '''
              export new_image="$DOCKERHUB_UN/image:${GIT_COMMIT}"
              render=$(cat ./k8s/app-deployment.yaml)
              echo "$render" | envsubst > ./k8s/app-deployment.yaml
              kubectl apply -f ./k8s/
              '''
            }
        }
        
    }
    
    post {
        success {
            slackSend message: "Pipeline is successful"
        }
        failure {
            slackSend message: "Pipeline has failed"
        }
    }       

}
    
