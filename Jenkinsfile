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
    registry = 'https://registry.hub.docker.com'
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
                    sh 'docker build -t iqm/javaapp:$BUID_NUMBER'
                    echo "-----------------docker build completed------------"
                }
            }  
        }

        stage('Docker Publish'){
            steps{
                script{
                echo "-----------------------Docker publish Started-----------"
                 docker.withRegistry("$registry", 'docker-hub'){
                    def app = docker.build("iqm/javaapp:$BUILD_NUMBER")
                    app.push()
                    }
                     echo "-----------------------Docker publish completed-----------"
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