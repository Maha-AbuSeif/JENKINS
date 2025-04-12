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
        
        
        stage('update db endpoint') {
            steps {
                withCredentials([string(credentialsId: 'db_endpoint', variable: 'db_endpoint')]) {
             sh '''
              export db_endpoint="$db_endpoint"
              render=$(cat ./k8s/mysql-service.yaml)
              echo "$render" | envsubst > ./k8s/mysql-service.yaml
             
              '''
                }
            }
        }
        
        stage('update db data in init container') {
    steps {
        withCredentials([
            string(credentialsId: 'DB_HOST', variable: 'DB_HOST'),
            string(credentialsId: 'DB_USER', variable: 'DB_USER'),
            string(credentialsId: 'DB_PASS', variable: 'DB_PASS'),
            string(credentialsId: 'DB_DATABASE', variable: 'DB_DATABASE')
        ]) {
            sh '''
                # Export the credentials
                export DB_HOST="$DB_HOST"
                export DB_USER="$DB_USER"
                export DB_PASS="$DB_PASS"
                export DB_DATABASE="$DB_DATABASE"

                # Read and substitute variables in deployment file
                render=$(cat ./k8s/app-deployment.yaml)
                echo "$render" | envsubst > ./app-deployment.yaml
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
              aws eks update-kubeconfig --name python-app-cluster --region us-west-2
              kubectl apply -f ./k8s/secrests.yaml
              kubectl apply -f ./k8s/mysql-service.yaml
              kubectl apply -f ./k8s/app-deployment.yaml
              kubectl apply -f ./k8s/app-loadbalancer-service.yaml
              '''
                }
            }
        }
        
    }
    

}
    

