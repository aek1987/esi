pipeline {
    agent any

    stages {

        stage('Init') {
            steps {
                // Nettoyer le projet
                bat 'mvn clean'
            }
        }



        stage('Test') {
            steps {
                // Exécute les tests et publie les résultats JUnit
               junit 'target/surefire-reports/*.xml'

            }
        }
        stage('Build') {
            steps {
                // Compiler et packager le projet Maven
                bat 'mvn package'

                // Archive les fichiers JAR générés
                archiveArtifacts artifacts: 'target/*.jar', allowEmptyArchive: true
            }
        }

       
        stage('documotation') {
            steps {
               bat: 'mvnw javadoc :javadoc'
            }
        }

    }
}
