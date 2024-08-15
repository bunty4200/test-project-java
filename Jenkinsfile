/*pipeline {
    agent {
        node {
            label 'maven'
        }
    }

    stages {
        stage('Clone-code') {
            steps {
                git 'https://github.com/bunty4200/test-project-java.git'
            }
        }
    }
}
*/

pipeline{
    agent{
        node{
            label 'maven'
        }
    }
environment {
    PATH = "/opt/apache-maven-3.9.8/bin:$PATH"
}
    stages{
        stage('build'){
            steps{
                sh 'mvn clean deploy -Dmaven.test.skip=true'
            }
        }
    }
}