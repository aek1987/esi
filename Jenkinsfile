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


           def result = sh(
               script: """
                   curl -s -o response.json -w "%{http_code}" http://localhost:8082/actuator/health || echo "000"
               """,
               returnStdout: true
           ).trim()


           def httpCode = result


           echo "HTTP Code: ${httpCode}"


           if (httpCode == "200") {


               def body = readFile('response.json')
               echo "Body: ${body}"


               if (body.contains('"status":"UP"')) {
                   echo "Application is healthy ✅"
               } else {
                    currentBuild.result = 'FAILURE'
               }


           } else {
               echo "Application not reachable"
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
