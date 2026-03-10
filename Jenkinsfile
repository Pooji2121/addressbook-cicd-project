pipeline {
    agent any

    tools {
        maven 'Maven'
    }

    environment {
        SONAR_SERVER = "sonar-server"
        SONAR_TOKEN = credentials('sonarqube-token')
    }

    stages {

        stage('Checkout Code') {
            steps {
                git 'https://github.com/Pooji2121/addressbook-cicd-project.git'
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
                    sh """
                    mvn sonar:sonar \
                    -Dsonar.projectKey=addressbook \
                    -Dsonar.host.url=http://13.221.57.54:9000 \
                    -Dsonar.login=${SONAR_TOKEN}
                    """
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
                curl -u admin:admin \
                "http://13.221.57.54:9000/api/issues/search?componentKeys=addressbook" \
                -o sonar-report.json
                '''
            }
        }

        stage('Upload Report to GitHub') {
            steps {
                sh '''
                git config --global user.name "jenkins"
                git config --global user.email "jenkins@example.com"

                git add sonar-report.json
                git commit -m "Added SonarQube report"
                git push origin master
                '''
            }
        }

        stage('Package WAR') {
            steps {
                sh 'mvn clean package'
            }
        }

        stage('Deploy to Tomcat') {
            steps {
                sh '''
                sudo cp target/addressbook.war /home/ubuntu/devops/apache-tomcat-9.0.115/webapps/
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

Job: ${env.JOB_NAME}
Build Number: ${env.BUILD_NUMBER}
Status: SUCCESS
Time: ${new Date()}
""",
                to: "poojinuthalapati09@gmail.com"
            )
        }

        failure {
            emailext(
                subject: "FAILED: ${env.JOB_NAME} #${env.BUILD_NUMBER}",
                body: """
Build Failed

Job: ${env.JOB_NAME}
Build Number: ${env.BUILD_NUMBER}
Status: FAILED
Time: ${new Date()}
""",
                to: "poojinuthalapati09@gmail.com"
            )
        }
    }
}
