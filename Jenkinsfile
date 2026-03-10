pipeline {
    agent any

    tools {
        maven 'Maven'
    }

    stages {

        stage('Checkout Code') {
            steps {
                git 'https://github.com/akshu20791/addressbook-cicd-project'
            }
        }

        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('sonar-server') {
                    sh 'mvn clean verify sonar:sonar'
                }
            }
        }

        stage('Build') {
            steps {
                sh 'mvn clean package'
            }
        }

        stage('Deploy to Tomcat') {
            steps {
                sh 'sudo mv /var/lib/jenkins/workspace/addressbook-pipeline/target/addressbook.war /home/ubuntu/apache-tomcat-8.5.100/webapps/'
'
            }
        }

    }
}
