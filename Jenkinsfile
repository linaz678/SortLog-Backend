pipeline {
    agent any
    
    environment {
        AWS_CRED = "AWS_sortlog"
        AWS_REGION = "ap-southeast-2"

        SORTLOG_DEV_REPO = "sortlog-dev"
        SORTLOG_PROD_REPO = "sortlog-prod"

        IMAGE_DEV = "$SORTLOG_DEV_REPO"
        IMAGE_PROD = "$SORTLOG_PROD_REPO"

        IMAGE_TAG = "${env.BUILD_TAG}"
        ECR_URL = "003374733998.dkr.ecr.ap-southeast-2.amazonaws.com"
    } 


        //Install denpendencies 
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

        stage ('Test') {
            steps {
                echo "Testing...."
            }
        }

         stage('Build Docker Image and Image Updating to ECR'){

            steps {
                withAWS(credentials: AWS_CRED, region: AWS_REGION){

                    script {
                        if(currentBuild.result != null && currentBuild.result != 'SUCCESS'){
                            return false
                        }

                        if (env.BRANCH_NAME == 'dev' ){
                            withEnv(["IMAGE_NAME=$IMAGE_DEV"]){
                                echo "IMAGE_Name = ${env.IMAGE_NAME}"
                            }
                        }

                        if (env.BRANCH_NAME == 'main'){
                            withEnv(["IMAGE_NAME=$IMAGE_PROD"]){
                
                                echo "IMAGE_Name = ${env.IMAGE_NAME}"
                            }
                        }

                        echo "Building and Uploading Docker Image to ECR"
                        script {
                        sh '''
                            docker build -t ${IMAGE_NAME} .
                            docker images --filter reference=${env.IMAGE_NAME}
                            aws ecr get-login-password --region $AWS_REGION | docker login --username AWS --password-stdin $ECR_URL
                            docker tag ${env.IMAGE_NAME}:$IMAGE_TAG $ECR_URL/$IMAGE_Name:$IMAGE_TAG
                            docker push $ECR_URL/$IMAGE_Name:$IMAGE_TAG
                        '''
                        
                        }                    
                    }
                }
            }
        } 

    }
    
    post {
        always {
            script {
                try{
                    // docker images -qa | xargs docker rmi -f
                    sh'''
                        docker rmi $(docker images -q)
                        docker system prune
                        cleanWs()
                    '''
                } catch (Exception e) {
                    echo "docker clean failed"
                }
            }
        
        }

        failure {
            // send message it was failsure
            echo "uhm... 我觉得不太行！"
        }

        success {
            // send message it was success
            echo "老铁！恭喜你，成功了呀!"
        }
    }
}
