pipeline {
    agent any

 environment {
       ROLLBACK_TAG = "v1.0.0"   // Set your stable rollback tag here
       ROLLBACK_BRANCH = "rollback/hotfix-1.1.0"
   }
    stages {

        stage('Init code') {
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

   stage('Deploy') {
            when {
                branch 'master'
            }
            steps {
                echo 'Deploying application...'

                // Stop and remove containers safely
                bat 'docker-compose down --remove-orphans'
                bat 'docker rm -f spring-boot-app || exit 0'
                bat 'docker rm -f mysql-db || exit 0'

                // Rebuild and start
                bat 'docker-compose up --build -d'
            }
        }

        stage('Health Check') {
            steps {
                echo "Checking Health..."
                sleep time: 15, unit: 'SECONDS'

                script {

                    def httpCode = bat(script: '''
                                        @echo off
                                        setlocal
                                
                                        curl -s -o response.json -w "%%{http_code}" http://localhost:8082/actuator/health > status.txt 2>nul
                                
                                        if errorlevel 1 (
                                            echo 000 > status.txt
                                        )
                                
                                        set /p code=<status.txt
                                        echo %code%
                                
                                        exit /b 0
                                        ''',
                            returnStdout: true).trim()

                    echo "HTTP Code: ${httpCode}"

                    if (httpCode == "200") {

                        def body = readFile('response.json')
                        echo "Body: ${body}"

                        if (body.contains('"status":"UP"')) {
                            echo "Application is healthy ✅"
                        } else {
                            error("Health endpoint returned non-UP status")
                        }

                    } else {
                        currentBuild.result = "FAILURE"


                    }
                    echo currentBuild.result
                }
            }
        }

        stage('Rollback') {
            when {
                expression { currentBuild.result == "FAILURE" }
            }
            steps {
                /*  def stableTag = sh(
                         script: "git tag --sort=-creatordate | head -n 1",
                         returnStdout: true
                     ).trim()
                 echo "pro stable ${stableTag}" */
                echo "Starting rollback to tag: ${ROLLBACK_TAG}"
                script {
                 // 1️⃣ Récupérer tous les tags depuis le remote
bat 'git fetch origin --tags --force'

// 2️⃣ Créer et basculer sur la branche depuis le tag
// 1️⃣ Créer une branche locale à partir du tag
bat "git branch ${ROLLBACK_BRANCH} ${ROLLBACK_TAG}"

// 2️⃣ Basculer sur cette nouvelle branche
bat "git checkout ${ROLLBACK_BRANCH}"

                    echo "Rolled back to tag ${ROLLBACK_TAG} on new branch ${ROLLBACK_BRANCH}"


                    //sh './deploy.sh'
                    bat './mvnw clean'
                    bat './mvnw install'

                    // Stop and remove containers safely
                    bat 'docker-compose down --remove-orphans'
                    bat 'docker rm -f spring-boot-app || exit 0'
                    bat 'docker rm -f mysql-db || exit 0'

                    // Rebuild and start
                    bat 'docker-compose up --build -d'

                    echo "Rollback deployment complete"




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
