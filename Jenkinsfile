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
        script {
            def maxRetries = 10
            def waitSeconds = 5
            def isHealthy = "UNHEALTHY"

            for (int i = 1; i <= maxRetries; i++) {
                echo "Attempt ${i}/${maxRetries}..."

                // Vérifie si le port 8082 répond
                def portOpen = bat(
                    script: 'powershell -Command "Test-NetConnection -ComputerName localhost -Port 8082 -WarningAction SilentlyContinue | Select-Object -ExpandProperty TcpTestSucceeded"',
                    returnStdout: true
                ).trim()

                if (portOpen.toLowerCase() == "true") {
                    echo "Port 8082 is open, sending health request..."
                    // Exécute curl
                    bat 'curl -s -o response.json http://localhost:8082/actuator/health || echo 000'

                    // Vérifie si response.json contient "UP"
                    isHealthy = bat(
                        script: 'findstr /C:"UP" response.json >nul && echo HEALTHY || echo UNHEALTHY',
                        returnStdout: true
                    ).trim()
                    
                    if (isHealthy == "HEALTHY") {
                        echo "Application is healthy ✅"
                        break
                    }
                } else {
                    echo "Port 8082 not open yet..."
                }

                if (i < maxRetries) {
                    echo "Waiting ${waitSeconds}s before retry..."
                    sleep time: waitSeconds, unit: 'SECONDS'
                }
            }

            if (isHealthy != "HEALTHY") {
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
