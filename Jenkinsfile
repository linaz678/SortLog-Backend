pipeline {
     agent any
<<<<<<< HEAD
     
        //Install denpendencies 
=======

    environment{
        CI ='true'
        AWS_CRED        = 'AWS_sortlog' //Change to yours
        AWS_REGION      = 'ap-southeast-2'
    }

>>>>>>> fe171b930329ba6cd46afbbb46f9e811f373eda3
    stages{
        stage('Install dependency')
        {
            steps{
             echo "Installing packages"
<<<<<<< HEAD
             sh 'yarn install --force'
             }          
=======
             sh 'yarn install'
             
             }
             
>>>>>>> fe171b930329ba6cd46afbbb46f9e811f373eda3
        }

        stage('yarn build') 
        {
            steps{
<<<<<<< HEAD
             sh "yarn export"
             sh 'ls -la ./out'
             }
        } 
    }
}
=======
             sh "yarn build "
             sh 'ls -la ./dist'
            //  sh 'sudo rm -r ./data'
             }
        } 
         stage('Build Docker image') {
            steps {
                sh 'docker build -t sortlogback .'
                sh 'docker images --filter reference=sortlogback'
            }
        }
        // stage('Run Docker Container') 
        // {
        //     environment{MONGO_URL=credentials('MONGO_URL')}
        //     steps {
        //         sh 'docker-compose up '
        //     }
        //   }


        stage('upload backend to  ECR bucket') {
            steps {
                withAWS(credentials: AWS_CRED, region: AWS_REGION)        
               
                {
                    echo "deploy to ECR "
                    sh '''
                    docker tag sortlogback 003374733998.dkr.ecr.ap-southeast-2.amazonaws.com/sortlog-repository
                    docker login -u AWS -p $(aws ecr get-login-password --region ap-southeast-2) 003374733998.dkr.ecr.ap-southeast-2.amazonaws.com/sortlog-repository
                    docker push 003374733998.dkr.ecr.ap-southeast-2.amazonaws.com/sortlog-repository
                    '''}
             
            }
         
         }

    }
}
>>>>>>> fe171b930329ba6cd46afbbb46f9e811f373eda3
