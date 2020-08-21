pipeline {
    agent any
        stages  {
            stage ('Build do Backend') {
                steps {
                    bat 'mvn clean package -DskipTests=true'
                    }
                }
            }
}