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
                sh '/mnt/c/maven/bin/mvn package -DskipTests'
            }
        }
        stage('Deploy') {
            steps {
                sh '''
                    pkill -f country-service-0.0.1-SNAPSHOT.jar || true
                    mkdir -p /var/lib/jenkins/deploy
                    cp target/country-service-0.0.1-SNAPSHOT.jar /var/lib/jenkins/deploy/app.jar
                    JENKINS_NODE_COOKIE=dontKillMe BUILD_ID=dontKillMe nohup java -jar /var/lib/jenkins/deploy/app.jar --server.port=9090 > /var/lib/jenkins/deploy/app.log 2>&1 &
                    sleep 20
                    curl -s http://localhost:9090/getcountries
                '''
            }
        }
    }
}
