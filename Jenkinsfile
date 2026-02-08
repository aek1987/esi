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
                // Publie les résultats JUnit
                junit testResults: 'target/surefire-reports/*.xml', allowEmptyResults: true
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


        stage('Documentation') {
            steps {
                // Génère la documentation Javadoc
                bat 'mvn javadoc:javadoc'

                // Archive le dossier de documentation
                archiveArtifacts artifacts: 'target/site/**', allowEmptyArchive: true
            }
        }

    }
}
