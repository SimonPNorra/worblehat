pipeline {
    agent any
    tools {
        maven 'maven-3.3.3'
        jdk 'JDK-8'
    }

    stages {
        stage('BUILD') {
            steps {
                checkout scm
                sh 'mvn clean install -Pcoverage'
            }
        }

        stage('QUALITY') {
            steps {
                sh 'mvn sonar:sonar -Pjenkins'
            }
        }

        stage('DEPLOY_DEV') {
            steps {
                echo 'mvn sonar:sonar -Pjenkins'
            }
        }

        stage('QUALITY') {
            steps {
                echo 'mvn sonar:sonar -Pjenkins'
            }
        }

        stage('DEPLOY_PROD') {
            steps {
                echo 'mvn sonar:sonar -Pjenkins'
            }
        }
    }
}