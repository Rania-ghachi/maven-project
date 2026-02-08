pipeline {

agent any
stages {
stage('test') {
steps{
bat 'C://apache-maven-3.9.12//bin//mvn clean package'
archiveArtifacts 'target/*.jar'
junit 'target/surefire-reports/*.xml'
}
}
stage('build') {
steps{
bat 'C://apache-maven-3.9.12//bin//mvn clean package'
archiveArtifacts 'target/*.jar'
}
}
}
}