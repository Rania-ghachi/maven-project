pipeline {
    agent any
    environment {
           ROLLBACK_TAG = "v1.0.0"   // Set your stable rollback tag here
           ROLLBACK_BRANCH = "rollback/hotfix-1.0.0"
       }

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
                           /*  stage('Unit Testing2') {
                                steps {
                                                bat 'mvn test'

                                       }
                            } */
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




        //Roolback stage


        stage('Rollback') {
                   when {
                       expression { currentBuild.result == 'FAILURE' }
                   }
                   steps {
                      /*  def stableTag = bat(
                               script: "git tag --sort=-creatordate | head -n 1",
                               returnStdout: true
                           ).trim()
                       echo "pro stable ${stableTag}" */
                       echo "Starting rollback to tag: ${ROLLBACK_TAG}"
                       script {
                           bat """
                                git fetch origin --tags --force
                                git checkout tags/${ROLLBACK_TAG} -b ${ROLLBACK_BRANCH}
                           """
                           echo "Rolled back to tag ${ROLLBACK_TAG} on new branch ${ROLLBACK_BRANCH}"


                           //sh './deploy.sh'
                           bat "mvn clean package"
                           // Stop and remove containers safely
                            bat 'docker-compose down --remove-orphans'
                            bat 'docker rm -f spring-boot-app || exit 0'
                            bat 'docker rm -f mysql-db || exit 0'

                            // Rebuild and start
                            bat 'docker-compose up --build -d'
                           echo "Rollback deployment complete"

                       }
                   }
               }


    }
}
