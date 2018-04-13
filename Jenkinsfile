pipeline {
    agent any
    tools {
        maven 'Maven'
        jdk 'JDK'
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