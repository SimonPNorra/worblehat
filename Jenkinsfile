pipeline {
    agent any
    triggers {
        pollSCM '* 7-19 * * 1-5'
    }
    environment {
        SONAR_URL = 'http://localhost:9000/sonar'
    }

    stages {
        stage('BUILD') {
            steps {
                sh 'mvn -B clean install -Pcoverage'
            }
        }

        stage('QUALITY') {
            steps {
                sh 'mvn -B sonar:sonar -Pjenkins'
            }
        }

        stage('DEPLOY_DEV') {
            when {
                branch 'master'
            }
            steps {
                sh "sudo /etc/init.d/worblehat-test stop"
                sh "mvn -B -f worblehat-domain/pom.xml liquibase:update -Pjenkins " +
                        "-Dpsd.dbserver.url=jdbc:mysql://localhost:3306/worblehat_test " +
                        "-Dpsd.dbserver.username=worblehat " +
                        "-Dpsd.dbserver.password=worblehat"

                sh "cp ${env.WORKSPACE}/worblehat-web/target/*.jar /opt/worblehat-test/worblehat.jar"
                sh "sudo /etc/init.d/worblehat-test start"
            }
        }

        stage('ACCEPTANCE_TEST') {
            when {
                branch 'master'
            }
            steps {
                sh 'mvn -B verify -Pjenkins'
            }
        }

        stage('DEPLOY_PROD') {
            when {
                branch 'master'
            }
            steps {
                sh "sudo /etc/init.d/worblehat-prod stop"
                sh "mvn -B -f worblehat-domain/pom.xml liquibase:update " +
                        "-Dpsd.dbserver.url=jdbc:mysql://localhost:3306/worblehat_prod " +
                        "-Dpsd.dbserver.username=worblehat " +
                        "-Dpsd.dbserver.password=worblehat"
                sh "cp ${env.WORKSPACE}/worblehat-web/target/*.jar /opt/worblehat-prod/worblehat.jar"
                sh "sudo /etc/init.d/worblehat-prod start"
            }
        }
    }
}