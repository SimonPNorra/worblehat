pipeline {
    agent any
    triggers {
        pollSCM '* 7-19 * * 1-5'
    }
    environment {
        SONAR_URL = 'http://localhost:9000/sonar'
        SITE_DEPLOY_PATH = '/scrumfordevelopers/nginx_root/worblehat-site'
    }

    stages {
        stage('BUILD') {
            steps {
                sh 'mvn -B clean install -Pcoverage'
            }
            post {
                always {
                    junit '**/target/surefire-reports/*.xml'
                }
            }
        }

        stage('QUALITY') {
            when {
                branch 'master'
            }
            steps {
                sh "mvn -B sonar:sonar -Pjenkins"
            }
        }

        stage('REPORTING') {
            when {
                branch 'master'
            }
            steps {
                sh 'mvn -B site:site site:stage'
                sh 'cp -r target/staging/. ${SITE_DEPLOY_PATH}/site'
            }
        }

        stage('DEPLOY DEV') {
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

        stage('ACCEPTANCE TEST') {
            when {
                branch 'master'
            }
            steps {
                sh 'mvn -B verify -Pjenkins -Pheadless -Pinclude-acceptancetests -Dapplication.url=http://localhost/worblehat-test'
                publishHTML(
                        [allowMissing         : false,
                         alwaysLinkToLastBuild: false,
                         keepAll              : false,
                         reportDir            : 'worblehat-acceptancetests/target/jbehave/view',
                         reportFiles          : 'report.html',
                         reportName           : 'Worblehat Acceptance Test Report',
                         reportTitles         : 'Worblehat Acceptance Test Report']
                )
            }
        }

        stage('DEPLOY PROD') {
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