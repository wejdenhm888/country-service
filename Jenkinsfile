*pipeline {
    agent any
    options { timeout(time: 15, unit: 'MINUTES') }
    tools {
        jdk 'JDK21'
    }
    stages {
        stage('Compile, test code, package in jar file') {
            steps {
                sh '/mnt/c/maven/bin/mvn clean package'
            }
            post {
                success {
                    junit allowEmptyResults: true, testResults: '**/target/surefire-reports/*.xml'
                }
            }
        }
        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv(installationName: 'MySonarQubeServer', credentialsId: 'sonarqubePWD') {
                    sh '/mnt/c/maven/bin/mvn sonar:sonar -Dsonar.projectKey=country-service -Dsonar.projectName=country-service'
                }
            }
        }
        stage('Deploy') {
            steps {
                sh '''
                    pkill -f /var/lib/jenkins/deploy/app.jar || true
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
