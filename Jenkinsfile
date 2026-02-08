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
    steps {
        bat '''
        if not exist doc mkdir doc
        xcopy target\\site\\* doc\\ /E /I /Y
        powershell Compress-Archive -Path doc\\* -DestinationPath doc.zip -Force
        '''
        archiveArtifacts 'doc.zip'
    }
}

}
}