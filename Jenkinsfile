pipeline {
    agent any

    tools {
        maven 'Maven'
    }

    environment {
        SONAR_SERVER = 'http://13.221.57.54:9000'
    }

    stages {

        stage('Checkout Code') {
            steps {
                git 'https://github.com/akshu20791/addressbook-cicd-project'
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
                withSonarQubeEnv('SonarQube') {
                    sh '''
                    mvn sonar:sonar \
                    -Dsonar.projectKey=addressbook \
                    -Dsonar.host.url=$SONAR_SERVER \
                    -Dsonar.login=admin
                    '''
                }
            }
        }

        stage('Quality Gate') {
            steps {
                timeout(time: 2, unit: 'MINUTES') {
                    waitForQualityGate abortPipeline: true
                }
            }
        }

        stage('Download Sonar Report') {
            steps {
                sh '''
                curl -u admin:admin $SONAR_SERVER/api/issues/search?componentKeys=addressbook > sonar-report.json
                '''
            }
        }

        stage('Upload Report to GitHub') {
            steps {
                sh '''
                git config --global user.email "jenkins@example.com"
                git config --global user.name "Jenkins"

                git add sonar-report.json || true
                git commit -m "Add SonarQube report" || true
                git push origin master || true
                '''
            }
        }

        stage('Package WAR') {
            steps {
                sh 'mvn package'
            }
        }

        stage('Deploy to Tomcat') {
            steps {
                sh '''
                cp target/addressbook.war /home/ubuntu/devops/apache-tomcat-9.0.115/webapps/
                '''
            }
        }
    }

    post {

        success {
            emailext(
                subject: "SUCCESS: ${env.JOB_NAME} #${env.BUILD_NUMBER}",
                body: """
Build Successful

Job Name: ${env.JOB_NAME}
Build Number: ${env.BUILD_NUMBER}
Status: SUCCESS
Time: ${new Date()}

Application URL:
http://13.222.44.218:8085/addressbook
""",
                to: "poojinuthalapati09@gmail.com"
            )
        }

        failure {
            emailext(
                subject: "FAILED: ${env.JOB_NAME} #${env.BUILD_NUMBER}",
                body: """
Build Failed

Job Name: ${env.JOB_NAME}
Build Number: ${env.BUILD_NUMBER}
Status: FAILED
Time: ${new Date()}
""",
                to: "poojinuthalapati09@gmail.com"
            )
        }
    }
}
