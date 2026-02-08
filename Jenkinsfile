pipeline {
    agent any

    stages {

        stage('Init') {
            steps {
                // Nettoyer le projet
                bat 'mvn clean'
            }
        }



        

         stage('Build') {
            steps {
                bat 'mvn package'
                archiveArtifacts artifacts: 'target/*.jar', allowEmptyArchive: true
            }
            post {
                success {
                    echo "✅ Build success"
                }
                failure {
                    echo "❌ Build failure"
                }
            }
        }
        stage('Test') {
            steps {
                // Publie les résultats JUnit
                junit testResults: 'target/surefire-reports/*.xml', allowEmptyResults: true
            }
        }

  /*      stage('Documentation') {
            steps {
                // Génère la documentation Javadoc
                bat 'mvn javadoc:javadoc'

                // Créer un dossier doc si non existant
                bat 'if not exist doc mkdir doc'

                // Copier le contenu de target/site dans doc
                bat 'xcopy target\\site doc\\ /E /I /Y'

                // Compresser le dossier doc en ZIP
                bat 'powershell -Command "Compress-Archive -Path doc\\* -DestinationPath doc.zip -Force"'

                // Archive le fichier ZIP
                archiveArtifacts artifacts: 'doc.zip', allowEmptyArchive: true
            }
        }
*/
        stage('Publish Report') {
            steps {
                // Publier un rapport HTML
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
