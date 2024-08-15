pipeline {
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