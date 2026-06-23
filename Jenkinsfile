pipeline {
    agent any 
    environment {
        AWS_DEFAULT_REGION = 'us-east-1'
    }

    stages {
        /*
stage('Build') {
    agent {
        docker {
            image 'node:18-alpine'
            reuseNode true
        }
    }
    steps {
        sh '''
            ls -la
            node --version
            npm --version
            npm ci
            npm run build
            ls -la
        '''
    }
}
*/
        stage('AWS S3 Buck Execution !!') {
            agent {
                docker {
                    image 'amazon/aws-cli'
                    reuseNode true
                    args "-u root --entrypoint=''"
                }
            }
            environment {
                AWS_S3_BUCK = 'learn-mybuck-20260611'
            }
            steps {
                 withCredentials([usernamePassword(credentialsId: 'JENKINS_s3',
                        usernameVariable: 'AWS_ACCESS_KEY_ID',
                        passwordVariable: 'AWS_SECRET_ACCESS_KEY')]) {
                            sh '''
                             aws --version
                             aws s3 ls
                             aws s3 sync build s3://$AWS_S3_BUCK
                            '''
                        }
            }
        }

        stage('AWS ECS EXECUTION') {
            agent {
                docker {
                    image 'amazon/aws-cli'
                    reuseNode true
                    args "-u root --entrypoint=''"
                }
            }
            environment {
                AWS_S3_BUCK = 'learn-mybuck-20260611'
            }
            steps {
                withCredentials([usernamePassword(credentialsId: 'JENKINS_s3',
                        usernameVariable: 'AWS_ACCESS_KEY_ID',
                        passwordVariable: 'AWS_SECRET_ACCESS_KEY')]) {
                sh '''
                 yum install jq -y
                 REV_VALUE=$(aws ecs register-task-definition --cli-input-json file://aws/task-definition-1.json | jq '.taskDefinition.revision')
                 echo "Revision No. created = $REV_VALUE"
                 aws ecs update-service \
    --cluster educated-zebra-tmxmq8 \
    --service Jenktask_prod-service-3a198knc \
    --task-definition Jenktask_prod:$REV_VALUE
                 aws ecs wait services-stable --cluster educated-zebra-tmxmq8 --services Jenktask_prod-service-3a198knc
                '''
}
            }
        }      
    }
}
