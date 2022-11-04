pipeline {
     agent any

    environment{
        CI ='true'
        AWS_CRED        = 'AWS_sortlog' //Change to yours
        AWS_REGION      = 'ap-southeast-2'
    }

    stages{
        stage('Install dependency')
        {
            steps{
             echo "Installing packages"
             sh 'yarn install'
             
             }
        }
        stage('yarn build') 
        {
            steps{
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
        stage('TF Launch Instances'){
            
            steps {
                withAWS(credentials: AWS_CRED, region: AWS_REGION) {
                   
                    
                        sh '''
                            export APP_ENV="UAT"
                            terraform init -input=false
                            terraform workspace select ${APP_ENV} || terraform workspace new ${APP_ENV}
                            terraform apply \
                               -var="app_env=${APP_ENV}"\
                               --auto-approve
                        '''
                         script {
                                ECR_REPO_NAME = sh(returnStdout: true, script: "terraform output repository_url").trim()
                                AWS_ECS_CLUSTER = sh(returnStdout: true, script: "terraform output ECS_Cluster_NAME")
                                AWS_ECS_SERVICE = sh(returnStdout: true, script: "terraform output ECS_Service_NAME")
                }
            }
        }
        stage('Deliver for UAT') {
            steps {
                withAWS(credentials: AWS_CRED, region: AWS_REGION)   
               
                {
                    echo "deploy to ECR "
                    sh "echo ${ECR_REPO_NAME}"
                    strip() 
                    sh "echo ${AWS_ECS_CLUSTER}"
                    sh "echo ${AWS_ECS_SERVICE}"
                    sh "docker tag sortlogback ${ECR_REPO_NAME}"
                    sh"aws ecr get-login-password --region ap-southeast-2 | docker login --username AWS --password-stdin ${ECR_REPO_NAME}"
                    sh "docker push ${ECR_REPO_NAME}"
                    sh "aws ecs update-service --cluster ${AWS_ECS_CLUSTER} --service ${AWS_ECS_SERVICE} --force-new-deployment"
                    // sh "aws ecs update-service --cluster"sortlogfinal-sortlogcluster-UAT"  --service "sortlogfinal-sortlogservice-UAT" --force-new-deployment"
                }
            }
        }

    }
}

