def registry = 'https://hub.docker.io'
def imageName = 'hub.docker.io/name-docker/iqm'
def version = '1.1.1'

pipeline{
   
    agent{
        node{
            label 'maven'
        }
    }
environment {
    PATH = "/opt/apache-maven-3.9.8/bin:$PATH"
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
                    app = docker.build(imageName+":"+version)
                    echo "-----------------docker build completed------------"
                }
            }  
        }

        stage('Docker Publish'){
            steps{
                script{
                echo "-----------------------Docker publish Started-----------"
                 docker.withRegistry(registry, 'docker-hub'){
                    app.push()
                    }
            }
        }
    }
}
}