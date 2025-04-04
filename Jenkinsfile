pipeline {
    agent any
    
    environment { 
      DOCKERHUB_UN = mahaabuseif
    }
    
    stages {
        stage('Build') {
            steps {
              sh '''
              docker image build -t $DOCKERHUB_UN/image:${GIT_COMMIT} ./app
              '''
            }
        }
    }
}
