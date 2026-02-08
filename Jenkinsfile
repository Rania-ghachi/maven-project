pipeline {
    agent any

    stages {

        stage('build') {
            steps {
                bat 'C://apache-maven-3.9.12//bin//mvn package'
                archiveArtifacts 'target/*.jar'
            }
            post {
                failure {
                    emailtext(
                        subject: "Build echec",
                        body: "Le build a echoue",
                        to: "assia.cntsid@gmail.com"
                    )
                }
                success {
                    emailtext(
                        subject: "Build reussi",
                        body: "Le build a reussi",
                        to: "assia.cntsid@gmail.com"
                    )
                }
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

                publishHTML(target: [
                    allowMissing: false,
                    alwaysLinkToLastBuild: true,
                    keepAll: true,
                    reportDir: 'target/site/apidocs',
                    reportFiles: 'index.html',
                    reportName: 'Documentation'
                ])
            }
        }

    }
}
