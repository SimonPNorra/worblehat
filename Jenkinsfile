pipeline {
    agent any
    tools {
        maven 'maven-3.3.3'
        jdk 'JDK-8'
    }

    environment {
        SONAR_URL = 'http://localhost:9000/sonar'
    }

    stages {
        stage('BUILD') {
            steps {
                checkout scm
                sh 'mvn -B clean install -Pcoverage'
            }
        }

        stage('QUALITY') {
            steps {
                sh 'mvn -B sonar:sonar -Pjenkins'
            }
        }

        stage('DEPLOY_DEV') {
            lock "DEPLOY_DEV" {
                steps {
                    sh "sudo /etc/init.d/worblehat-test stop"
                    sh "mvn -B liquibase:update -Pjenkins"
                    sh "cp ${env.WORKSPACE}/worblehat-web/target/*.jar /opt/worblehat-test/worblehat.jar"
                    sh "sudo rm /tmp/spring.log"
                    sh "sudo /etc/init.d/worblehat-test start"
                }
            }
        }

        stage('ACCEPTANCE_TEST') {
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