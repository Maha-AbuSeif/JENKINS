pipeline {
    agent any
    
    environment { 
      DOCKERHUB_UN = 'mostafamedhat1'
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
               withCredentials([usernamePassword(credentialsId: 'Docker_Creds', usernameVariable: 'DOCKERHUB_UN', passwordVariable:  'DOCKERHUB_PASS')]){  
                   sh '''
                    docker login -u $DOCKERHUB_UN -p $DOCKERHUB_PASS
                    docker push $DOCKERHUB_UN/image:${GIT_COMMIT}
                   '''
               }
            }
       }
        
        stages {
        stage('update db endpoint') {
            steps {
                withCredentials([string(credentialsId: 'db-endpoint', variable: 'db-endpoint')]) {
             sh '''
              export db-endpoint=$db-endpoint
              render=$(cat ./k8s/mysql-service.yaml)
              echo "$render" | envsubst > ./k8s/mysql-service.yaml
             
              '''
                }
            }
        }
        stage('Deploy') {
            steps {
                withCredentials([[
                    $class: 'AmazonWebServicesCredentialsBinding',
                    credentialsId: 'aws-cred',  // Replace with your saved credentials ID
                    accessKeyVariable: 'AWS_ACCESS_KEY_ID',
                    secretKeyVariable: 'AWS_SECRET_ACCESS_KEY'
                ]]) {
              sh '''
              export new_image="$DOCKERHUB_UN/image:${GIT_COMMIT}"
              render=$(cat ./k8s/app-deployment.yaml)
              echo "$render" | envsubst > ./k8s/app-deployment.yaml
              kubectl apply -f ./k8s/
              '''
                }
            }
        }
        
    }
    
    post {
        success {
            // slackSend message: "Pipeline is successful"
        }
        failure {
           // slackSend message: "Pipeline has failed"
        }
    }       

}
    
