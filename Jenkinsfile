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
            def maxRetries = 5
            def waitSeconds = 10
            def isHealthy = "UNHEALTHY"
            
            for (int i = 1; i <= maxRetries; i++) {
                echo "Attempt ${i} of ${maxRetries}..."
                
                // Exécute curl
                bat "curl -s -o response.json http://localhost:8082/actuator/health || echo 000"
                
                // Vérifie si response.json contient "UP"
                isHealthy = bat(
                    script: 'findstr /C:"UP" response.json >nul && echo HEALTHY || echo UNHEALTHY',
                    returnStdout: true
                ).trim()
                
                echo "Health check result: ${isHealthy}"
                
                if (isHealthy == "HEALTHY") {
                    echo "Application is healthy ✅"
                    break
                } else if (i < maxRetries) {
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
