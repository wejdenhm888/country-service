pipeline {
    agent any
    tools {
        maven 'mymaven'
        jdk 'JDK21'
    }
    stages {
        stage('Checkout code') {
            steps {
                git branch: 'master', url: 'https://github.com/Jamina-ENSI/Country-service.git'
            }
        }
        stage('Compile, Test & JaCoCo Report') {
            steps {
                // Nettoie, lance les tests unitaires (Surefresh) et génère JaCoCo
                sh 'mvn clean test jacoco:report'
            }
            post {
                success {
                    // Récupère les rapports Surefire demandés dans le TP
                    junit allowEmptyResults: true, testResults: '**/target/surefire-reports/*.xml'
                }
            }
        }
        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv(installationName: 'MySonarQubeServer', credentialsId: 'sonarqubePWD') {
                    sh "mvn sonar:sonar -Dsonar.projectKey=country-service -Dsonar.projectName=country-service"
                }
            }
        }
        stage('Deploy to Nexus') {
            steps {
                // Envoie l'artefact (.jar / .war) et les rapports vers Nexus
                sh 'mvn deploy -DskipTests'
            }
        }
    }
}
