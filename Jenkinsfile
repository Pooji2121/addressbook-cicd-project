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

        stage('Compile the code') {
            steps {
                sh 'mvn clean compile'
            }
        }

        stage('Testing the code') {
            steps {
                sh 'mvn test'
            }
        }

        stage('QA of the code') {
            steps {
                sh 'mvn pmd:pmd'
            }
        }

        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('sonar-server') {
                    sh 'mvn sonar:sonar'
                }
            }
        }

        stage('Package') {
            steps {
                sh 'mvn package'
            }
        }

        stage('Deploy the project on tomcat') {
            steps {
                sh 'sudo cp /var/lib/jenkins/workspace/addressbook-pipeline/target/addressbook.war /home/ubuntu/apache-tomcat-8.5.100/webapps/'
            }
        }

    }
}
