pipeline {
    agent any
    tools {
        maven 'mymaven'
    }
    stages {
        stage('Compile code') {
            steps {
                sh 'mvn compile'
            }
        }
        stage('Test code') {
            steps {
                sh 'mvn test'
            }
            post {
                success {
                    junit allowEmptyResults: true, testResults: '**/target/surefire-reports/*.xml'
                }
            }
        }
        stage('Package code') {
            steps {
                sh 'mvn package'
            }
        }
    }
}
