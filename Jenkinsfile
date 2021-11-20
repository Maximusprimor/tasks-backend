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
                    bat "${scannerhome}/bin/sonar-scanner -e -Dsonar.projectKey=DeployBack -Dsonar.host.url=http://localhost:9000 -Dsonar.login=433aebd933b836100acbd2e055271ea6d02a3a3c -Dsonar.java.binaries=target -Dsonar.coverage.exclusions=**/.mvn/**,**/src/test/**,**/model/**,**Application.java"
                }
            }
        }
        stage ('Quality Gate') {
            steps {
                sleep(5)
                timeout(time: 1, unit: 'MINUTES') {
                    waitForQualityGate abortPipeline: true
                }
            }
        }
        stage ('Deploy Back-End') {
            steps {
                deploy adapters: [tomcat8(credentialsId: 'Tomcat_login', path: '', url: 'http://localhost:8001/')], contextPath: 'tasks-backend', war: 'target/tasks-backend.war'
            }
        }
        stage ('API Test') {
            steps {
                dir('api-test') {
                    git credentialsId: 'Github_login', url: 'https://github.com/Maximusprimor/tasks-api-tests.git'
                    bat 'mvn test'
                }
            }
        }
        stage ('Deploy Front-End') {
            steps {
                dir('front-end') {
                    git credentialsId: 'Github_login', url: 'https://github.com/Maximusprimor/tasks-frontend.git'
                    bat 'mvn clean package'
                    deploy adapters: [tomcat8(credentialsId: 'Tomcat_login', path: '', url: 'http://localhost:8001/')], contextPath: 'tasks', war: 'target/tasks.war'
                }                
            }
        }
        stage ('Functional Test') {
            steps {
                dir('functional-test') {
                    git credentialsId: 'Github_login', url: 'https://github.com/Maximusprimor/tasks-functional-tests.git'
                    bat 'mvn test'
                }
            }
        }        
    }
}