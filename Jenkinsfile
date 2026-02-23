pipeline {
    agent any

    stages {

        stage('build') {
            steps {
                bat 'C://apache-maven-3.9.12//bin//mvn package'
                archiveArtifacts 'target/*.jar'
            }
            /* post {
                failure {
                    mail(
                        subject: "Build echec",
                        body: "Le build a echoue",
                        to: "assia.cntsid@gmail.com"
                    )
                }
                success {
                    mail(
                        subject: "Build reussi",
                        body: "Le build a reussi",
                        to: "assia.cntsid@gmail.com"
                    )
                }
            }*/
        }

/*         stage('documentation') {
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
        } */


       /*    stage('parallel') {
                          parallel {
                            stage('Unit Testing1') {
                                steps {
                                          bat 'mvn javadoc:javadoc'
                                                publishHTML(target: [
                                                    allowMissing: false,
                                                    alwaysLinkToLastBuild: true,
                                                    keepAll: true,
                                                    reportDir: 'target/site/apidocs',
                                                    reportFiles: 'index.html',
                                                    reportName: 'Documentation'
                                                ])
                                            }
                            }*/
                            stage('Unit Testing2') {
                                steps {
                                                bat 'mvn test'

                                       }
                            }
                       //   }
             //   }





        stage('deploy') {
            when {
                branch 'master'
                  }
            steps {
                   bat 'docker-compose up --build -d'

                   //bat 'mvn deploy'
                    //archiveArtifacts 'target/*.jar'
                  }

        }


        //Health Stage


        stage('Health Check') {
           steps {
               echo "Checking Health..."
               sleep time: 10, unit: 'SECONDS'


               script {


                   def result = bat(
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






    }
}
