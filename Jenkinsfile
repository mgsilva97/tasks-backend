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
                    //bat "${scannerHome}/bin/sonar-scanner -e -Dsonar.projectKey=DeployBack -Dsonar.host.url=http://192.168.1.28:9000 -Dsonar.login=74d8d65125e831294764579d17572b4cccacf554 -Dsonar.java.binaries=target -Dsonar.coverage.exclusions=**/.mvn/**,**/src/test/**,**/model/**,**Application.Java"
                      bat "${scannerHome}/bin/sonar-scanner -e -Dsonar.projectKey=DeployBack -Dsonar.host.url=http://192.168.1.28:9000 -Dsonar.login=74d8d65125e831294764579d17572b4cccacf554 -Dsonar.java.binaries=target -Dsonar.coverage.exclusions=**/.mvn/**,**/src/test/**,**/model/**,**Application.Java"
                }
            }
        }
                stage ("Quality Gate") { 
                    steps {
                      script {
                        sleep(10)
                        timeout(time: 1, unit: 'MINUTES') {
                        // waitForQualityGate(abortPipeline: true, credentialsId: '081e7772735bbbeed002eebc846dd177a04a5e5a') 
                        waitForQualityGate abortPipeline: true //, credentialsId: '74d8d65125e831294764579d17572b4cccacf554') 
                             def qg = waitForQualityGate()
                             if (qg.status != 'OK') {
                             error "Pipeline abort due to quality gate failure ${qg.status}"
                        }
                    }
                }               
            }
        }
                  stage('Deploy Backend') {
                    steps {
                        deploy adapters: [tomcat8(credentialsId: 'TomcatLogin', path: '', url: 'http://ayrtonsenna.it:8001/')], contextPath: 'tasks-backend', war: 'target/tasks-backend.war'
                    } 
                            }
                  stage('API Tests') {
                      steps {
                        dir('api-test') {
                            git credentialsId: 'TomcatLogin', url: 'https://github.com/mgsilva97/tasks-api-test'
                            bat 'mvn test'
                        }
                    } 
                }

                stage('Deploy Frontend') {
                    steps {
                        dir('frontend') {
                        git credentialsId: 'TomcatLogin', url: 'https://github.com/mgsilva97/tasks-frontend'
                        bat 'mvn clean package -DskipTests=true'
                        deploy adapters: [tomcat8(credentialsId: 'TomcatLogin', path: '', url: 'http://ayrtonsenna.it:8001/')], contextPath: 'tasks', war: 'target/tasks.war'
        
                    } 
                }
            }
    }
}