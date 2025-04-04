pipeline {
    agent any
    
    environment { 
      DOCKERHUB_UN = 'mahaabuseif'
    }
    
    stages {
        stage('Build') {
            steps {
              sh '''
              docker image build -t $DOCKERHUB_UN/image:${GIT_COMMIT} ./app
              '''
            }
        }
         stage('Test') {
            steps {
              sh '''
              export new_image="$DOCKERHUB_UN/image:${GIT_COMMIT}"
              render=$(cat docker-compose.yml)
              echo "$render" | envsubst > docker-compose.yml
              docker-compose up 
              '''
            }
        }
        stage('Delivery') {
            steps {
               withCredentials([usernamePassword(credentialsId: 'Docker_Creds', usernameVariable: 'DOCKERHUB_UN', passwordVariable:  'DOCKERHUB_PASS')])  
                 sh '''
                    docker login -u $DOCKERHUB_UN -p $DOCKERHUB_PASS
                    docker push $DOCKERHUB_UN/image:${GIT_COMMIT}
                 '''
            }
       }
            
    }
    Post {
        success {
            slackSend message: "Pipeline is successful"
        }
        failure {
            slackSend message: "Pipeline has failed"
        }
    }       

}
    
