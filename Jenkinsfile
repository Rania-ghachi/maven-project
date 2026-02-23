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
                    echo 'Deploying application...'

                    // Stop and remove containers safely
                    bat 'docker-compose down --remove-orphans'
                    bat 'docker rm -f spring-boot-app || exit 0'
                    bat 'docker rm -f mysql-db || exit 0'

                    // Rebuild and start
                    bat 'docker-compose up --build -d'
                }

        }


        //Health Stage


        stage('Health Check') {
            steps {
                echo "Checking Health..."
                sleep time: 15, unit: 'SECONDS'

                script {
                    def result = bat(
                        script: 'curl -s -o response.json -w %%{http_code} http://localhost:8082/actuator/health',
                        returnStdout: true
                    ).trim()

                    def httpCode = result[-3..-1]

                    echo "HTTP Code: ${httpCode}"

                    if (httpCode == "200") {
                        def body = readFile('response.json')
                        if (body.contains('"status":"UP"')) {
                            echo "Application is healthy ✅"
                        } else {
                            error("Health endpoint returned DOWN")
                        }
                    } else {
                        error("Application not reachable (HTTP ${httpCode})")
                    }
                }
            }
        }





    }
}
