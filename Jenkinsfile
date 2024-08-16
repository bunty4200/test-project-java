pipeline{
    
    agent{
        node{
            label 'maven'
        }
    }

environment {
    PATH = "/opt/apache-maven-3.9.8/bin:$PATH"
    BUILD_NUMBER = "${env.BUILD_NUMBER ?: 'latest'}"
    registry = 'https://registry.hub.docker.com'
    DOCKERHUB_CREDENTIALS = credentials('docker-hub')
    AWS_REGION = 'ap-south-1'  // Update to your desired AWS region
    CLUSTER_NAME = 'my-ecs-cluster'  // Update with your ECS Cluster name
    SERVICE_NAME = 'my-ecs-service'  // Update with your ECS Service name
    TASK_DEFINITION = 'my-task-def'  // Update with your Task Definition family name
    CONTAINER_NAME = 'my-container'  // Update with your container name used in the task definition
}
    stages {
        stage('Build') {
            steps {
                echo "----------------------build  started-------------"
                sh 'mvn clean deploy -Dmaven.test.skip=true'
                echo "----------------------build  completed-------------"
            }
       }

        stage('test'){
            steps{
                echo "----------------------unit test started-------------"
                sh 'mvn surefire-report:report'
                echo "----------------------unit test completed------------"
            }
        }

        stage('Build Docker image'){
            steps{
                script{
                    echo "-----------------docker build started--------------"
                     sh 'docker build -t bunty4200/iqm_javaapp:${BUILD_NUMBER} .'
                    echo "-----------------docker build completed------------"
                }
            }  
        }


        stage('login to dockerhub') {
            steps{
                sh 'echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin'
            }
        }


        stage('push image'){
            steps{
                sh 'docker push bunty4200/iqm_javaapp:${BUILD_NUMBER}'
        }
    }


    stage('Deploy to ECS') {
            steps {
                    echo "-----------------Deploying to ECS started------------"

                    withCredentials([aws(credentialsId: 'aws-cred', region: "${AWS_REGION}")])
                {
                    // Register new task definition revision
                    sh """
                    aws ecs register-task-definition --region ${AWS_REGION} \
                    --family ${TASK_DEFINITION} \
                    --network-mode awsvpc \
                    --container-definitions '[
                        {
                            "name": "${CONTAINER_NAME}",
                            "image": "bunty4200/iqm_javaapp:${BUILD_NUMBER}",
                            "memory": 512,
                            "cpu": 256,
                            "essential": true,
                            "portMappings": [
                                {
                                    "containerPort": 8080,
                                    "hostPort": 8080,
                                    "protocol": "tcp"
                                }
                            ]
                        }
                    ]' \
                    --requires-compatibilities FARGATE \
                    --execution-role-arn arn:aws:iam::533267330681:role/ecsTaskExecutionRole \
                    --task-role-arn arn:aws:iam::533267330681:role/ecsTaskRole
                    """

                    // Update ECS service with the new task definition
                    sh """
                    aws ecs update-service --region ${AWS_REGION} \
                    --cluster ${CLUSTER_NAME} \
                    --service ${SERVICE_NAME} \
                    --force-new-deployment
                    """

                    echo "-----------------Deploying to ECS completed------------"
                }
            }
        }

}

post{
    always{
        sh 'docker logout'
    }
}
}
