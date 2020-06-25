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
    }
}


 
                