pipeline {
    agent any

    tools {
        maven 'Maven'
    }

    environment {
        SCANNER_HOME = tool 'SonarQubeScanner'
    }

    stages {

        stage('Checkout Code') {
            steps {
                git 'https://github.com/Pooji2121/addressbook-cicd-project'
            }
        }

        stage('Compile Code') {
            steps {
                sh 'mvn clean compile'
            }
        }

        stage('Test Code') {
            steps {
                sh 'mvn test'
            }
        }

        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('sonar-server') {
                    sh 'mvn sonar:sonar'
                }
            }
        }

        stage('Package WAR') {
            steps {
                sh 'mvn package'
            }
        }

        stage('Deploy to Tomcat') {
            steps {
                sh 'sudo cp target/addressbook.war /home/ubuntu/devops/apache-tomcat-9.0.115/webapps/'
            }
        }

    }

    post {

        success {
            echo 'Application Deployed Successfully!'
        }

        failure {
            echo 'Build Failed!'
        }

    }
}
