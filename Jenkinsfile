pipeline {
    agent any

    stages {

        stage('Init fff') {
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
        stage('Docker Build') {
            steps {
                bat 'docker-compose up --build -d'
            }
        }

stage('Health Check') {
    steps {
        echo "Checking Health..."
        sleep time: 10, unit: 'SECONDS'

        script {
            // Exécute curl et récupère la réponse dans response.json
            def curlResult = bat(
                script: 'curl -s -o response.json http://localhost:8082/actuator/health || echo 000',
                returnStatus: true
            )

            // Vérifie si le fichier response.json existe et contient "UP"
            def isHealthy = bat(
                script: 'findstr /C:"UP" response.json >nul && echo HEALTHY || echo UNHEALTHY',
                returnStdout: true
            ).trim()

            echo "Health check result: ${isHealthy}"

            if (isHealthy == "HEALTHY") {
                echo "Application is healthy ✅"
            } else {
                echo "Application not reachable or not healthy ❌"
                currentBuild.result = 'FAILURE'
            }
        }
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



 /*  post {
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
}*/

}

/*** Edited by lounis 2222 ***/
/*aaaaa*/
