pipeline {
    agent any
        stages  {
            stage ('Build do Backend') {
                steps {
                    bat 'mvn clean package -DskipTests=true'
                    }
                }

                stage ('Unit Tests') {
                steps {
                    bat 'mvn test'
                    }
                }

                stage ('Sonar Analysis') {
                    environment {
                        scannerHome = tool 'SONAR_SCANNER'

                    }
                steps {
                    withSonarQubeEnv('SONAR_REMOTE') {
                    bat "${scannerHome}/bin/sonar-scanner -e -Dsonar.projectKey=DeployBack -Dsonar.host.url=http://http://192.168.1.28:9000 -Dsonar.login=74d8d65125e831294764579d17572b4cccacf554 -Dsonar.java.binaries=target -Dsonar.coverage.exclusions=**/.mvn/**,**/src/test/**,**/model/**,**Application.Java"
                }
            }
        }
                stage ('Quality Gate') { 
                    steps {
                        sleep(10)
                        timeout(time: 1, unit: 'HOURS') {
                        waitForQualityGate abortPipeline: true //, credentialsId: '74d8d65125e831294764579d17572b4cccacf554') 
                       // def qg = waitForQualityGate()
                        //if (qg.status != 'OK') {
                       //error "Pipeline abort due to quality gate failure ${qg.status}"
                        //}
                    }
                }               
            }
        }
    }