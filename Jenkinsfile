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
              docker compose up 
              '''
            }
        }
        
    }
}
