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
                    echo "-----------------docker build started--------------"
                    sh 'docker build -t iqm/javaapp:$BUILD_NUMBER .'
                    echo "-----------------docker build completed------------"
            }  
        }
    }
}