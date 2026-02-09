pipeline {
    agent any

    stages {

        stage('Init') {
            steps {
                bat 'mvn clean'
            }
        }

        stage('Test and Report') {
            parallel {
                stage('Test') {
                    steps {
                        junit testResults: 'target/surefire-reports/*.xml',
                              allowEmptyResults: true
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
        }

        stage('Build') {
            when {
                branch 'main'   // ne s'exécute que si la branche est main
            }
            steps {
                bat 'mvn package'
                archiveArtifacts artifacts: 'target/*.jar', allowEmptyArchive: true
            }
        }

        stage('Docker Build') {
            when {
                branch 'main'   // ne s'exécute que si la branche est main
            }
            steps {
                bat 'docker-compose up --build -d'
            }
        }

    }

    /* Post actions
    post {
        success {
            echo "🎉 PIPELINE SUCCESS"
            mail(
                subject: "Build success",
                body: "Build succeeded",
                to: "nekaaabdelkader1987@gmail.com"
            )
        }
        failure {
            echo "🔥 PIPELINE FAILED"
        }
    }
    */
}
