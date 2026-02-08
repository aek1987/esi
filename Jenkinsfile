pipeline {
    agent any

    stages {

        stage('Init') {
            steps {
                bat 'mvn clean'
            }
        }

        stage('Test') {
            steps {
                junit testResults: 'target/surefire-reports/*.xml',
                      allowEmptyResults: true
            }
        }

        stage('Build') {
            steps {
                bat 'mvn package'
                archiveArtifacts artifacts: 'target/*.jar', allowEmptyArchive: true
            }
           
        }

        stage('Publish Report') {
            steps {
                publishHTML(target: [
                    allowMissing: false,
                    alwaysLinkToLastBuild: true,
                    keepAll: true,
                    reportDir: 'target/site/apidocs',
                    reportFiles: 'index.html',
                    reportName: 'My Reports',
                    reportTitles: 'The Report'
                ])
            }
        }
    }

    post {
        success {
            echo "🎉 PIPELINE SUCCESS"
           mail (   subject: "Build success",    body: "Build succeeded",
    to: "nekaaabdelkader1987@gmail.com"
)

        }
        failure {
            echo "🔥 PIPELINE FAILED"
        }
    }
}
