pipeline{
    agent any
    stages{
        stage('Build Backend'){
            steps{
                bat 'mvn clean package -DskipTests=true'
            }
        }
        stage('Unit Test'){
            steps{
                bat 'mvn test'
            }
        }
         stage('Sonar Analysis'){
             environment {
                 scannerHome = tool 'SONAR_SCANNER'
             }
            steps{
                withSonarQubeEnv('SONAR_LOCAL'){
                    bat "${scannerHome}/bin/sonar-scanner -e -Dsonar.projectKey=DeployBack -Dsonar.host.url=http://localhost:9000 -Dsonar.login=eb13ec1ab643591ba7c432e892adfc69e5b2237a -Dsonar.java.binaries=target"
                }
               
            }
        }
        stage('Quality Gate'){
            steps{
                sleep(5) 
                timeout(time: 1, unit: 'MINUTES'){
                    waitForQualityGate abortPipeline: true
                }
            }
        }
         stage('Deploy Backend'){
            steps{
                deploy adapters: [tomcat8(credentialsId: 'TomcatLogin', path: '', url: 'http://localhost:8001/')], contextPath: 'tasks-backend', war: 'target/tasks-backend.war'
            }
        }
         stage('API Test'){
            steps{
                dir('api-test'){
                    git 'https://github.com/mssouza/tasks-api-test'
                    bat 'mvn test'
                }
            }
        }
         stage('Deploy Frontend'){
            steps{
                dir('frontend'){
                    git 'https://github.com/mssouza/tasks-frontend'
                    bat 'mvn clean package'
                    deploy adapters: [tomcat8(credentialsId: 'TomcatLogin', path: '', url: 'http://localhost:8001/')], contextPath: 'tasks', war: 'target/tasks.war'
                }
            }
        }
        stage('Functional Test'){
            steps{
                dir('functional-test'){
                    git 'https://github.com/mssouza/tasks-functional-test'
                    bat 'mvn test'
                }
            }
        }
    }
}

 
                