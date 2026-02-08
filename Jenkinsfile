pipeline {

agent any
stages {
/* stage('init') {
steps{
bat 'C://apache-maven-3.9.12//bin//mvn clean'
}
}
stage('test') {
steps{
bat 'C://apache-maven-3.9.12//bin//mvn test'
junit 'target/surefire-reports *//*.xml'
}
} */
stage('build') {
steps{
bat 'C://apache-maven-3.9.12//bin//mvn package'
archiveArtifacts 'target/*.jar'
}
}

stage('documentation') {
steps{
bat '''
mkdir -p doc
cp -r 'target/site/* doc/'
zip -r doc.zip doc
'''
archiveArtifacts 'doc.zip'
}
}
}
}