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

                            def httpCode = bat(script: '''
                                                @echo off
                                                setlocal

                                                curl -s -o response.json -w "%%{http_code}" http://localhost:8082/actuator/health > status.txt 2>nul

                                                if errorlevel 1 (
                                                    echo 000 > status.txt
                                                )

                                                set /p code=<status.txt
                                                echo %code%

                                                exit /b 0
                                                ''',
                                    returnStdout: true).trim()

                            echo "HTTP Code: ${httpCode}"

                            if (httpCode == "200") {

                                def body = readFile('response.json')
                                echo "Body: ${body}"

                                if (body.contains('"status":"UP"')) {
                                    echo "Application is healthy ✅"
                                } else {
                                    error("Health endpoint returned non-UP status")
                                }

                            }  else {
                                currentBuild.result = "FAILURE"


                            }
                            echo "${currentBuild.result}"
                        }
                    }
                }

                stage('Rollback') {
                    when {
                        expression { currentBuild.result == "FAILURE" }
                    }
                    steps {
                        /*  def stableTag = sh(
                                 script: "git tag --sort=-creatordate | head -n 1",
                                 returnStdout: true
                             ).trim()
                         echo "pro stable ${stableTag}" */
                        echo "Starting rollback to tag: ${ROLLBACK_TAG}"
                        script {
                        bat """
                            git fetch origin --tags --force
                            git checkout ${ROLLBACK_BRANCH} || git checkout -b ${ROLLBACK_BRANCH}
                            git reset --hard tags/${ROLLBACK_TAG}
                        """


                            echo "Rolled back to tag ${ROLLBACK_TAG} on new branch ${ROLLBACK_BRANCH}"


                            //sh './deploy.sh'
                            bat 'mvn clean'
                            bat 'mvn install'

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
            post {
                success {
                    echo 'success'
                }
                failure {
                    echo 'failure'
                }
            }
        }
