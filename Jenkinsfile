pipeline {
    agent any
    stages {
        stage ('Build Backend') {
            steps {
                bat 'mvn clean package -DskipTests=true'
            }
        }
        stage ('Unit Tests') {
            steps {
                bat 'mvn test'
            }
        }
        stage ('Sonar Analisys') {
            environment {
                scannerhome = tool 'Sonar_Scanner'
            }
            steps {
                withSonarQubeEnv('Sonar_local') {                    
                    bat "${scannerHome}/bin/sonar-scanner -e -Dsonar.projectKey=DeployBack -Dsonar.host.url=http://localhost:9000 -Dsonar.login=433aebd933b836100acbd2e055271ea6d02a3a3c -Dsonar.java.binaries=target -Dsonar.coverage.exclusions=**/.mvn/**, **/src/test/**, **/model/**, **Application.java"
                }               
            }
        }        
    }
}