pipeline {
    agent any
    options { timeout(time: 10, unit: 'MINUTES') }
    tools {
        jdk 'JDK21'
    }
    stages {
        stage('Compile code') {
            steps {
                sh '/mnt/c/maven/bin/mvn compile'
            }
        }
        stage('Test code') {
            steps {
                sh '/mnt/c/maven/bin/mvn test'
            }
            post {
                success {
                    junit allowEmptyResults: true, testResults: '**/target/surefire-reports/*.xml'
                }
            }
        }
        stage('Package code') {
            steps {
                sh '/mnt/c/maven/bin/mvn package'
            }
        }
    }
}
