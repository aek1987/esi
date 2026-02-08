pipeline {

agent any
stages {

stage('build') {
steps {
bat 'mvn clean package'
 archiveArtifacts  artifact:'target/*.jar'
 
}
stage('test') {
steps {

 junit   :'/target/surfire-reports/*.xml'
 
}
}
}
}