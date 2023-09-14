pipeline{
    agent any
    stages{
        stage('Build Backend'){
            steps{
                bat 'mvn clean package -DskipTests=true'
            }
        }
        stage('Unit Tests'){
            steps{
                bat 'mvn test'
            }
        }
        stage('Sonar Analysis'){
            environment{
                scannerHome = tool 'SONAR_SCANNER'
            }
            steps{
                withSonarQubeEnv('SONAR_LOCAL'){
                    bat "${scannerHome}/bin/sonar-scanner -e -Dsonar.projectKey=tasks -Dsonar.host.url=http://localhost:9000 -Dsonar.login=fc15e6bc58611cb0d91772f333e47944f5127466 -Dsonar.java.binaries=target -Dsonar.coverage.exclusions=**/.mvn/**,**/src/test/**,**/model/**,**Application.java,**RootController.java"
                }
            }
        }
        stage('Quality Gate'){
            steps{
                sleep(33)
                timeout(time: 4, unit:'MINUTES'){
                    waitForQualityGate abortPipeline: true
                }  
            } 
        }
        stage('Deploy Backend'){
            steps{
                deploy adapters: [tomcat8(credentialsId: 'e88eb18b-d1a9-4437-8ee8-7c83a66adb87', path: '', url: 'http://localhost:8001/')], contextPath: 'tasks-backend', war: 'target/tasks-backend.war'
            }
        }
        stage('API test'){
            steps{
                dir('tasks-api-test') {
                        git credentialsId: 'github_login', url: 'https://github.com/CarvalhoQA/tasks-api-test'
                        bat 'mvn test'
                }
            }
        }
        stage('Deploy Frontend'){
            steps{
                dir('tasks-frontend') {
                       git credentialsId: 'github_login', url: 'https://github.com/CarvalhoQA/tasks-frontend'
                       bat 'mvn clean package -DskipTests=true'
                       deploy adapters: [tomcat8(credentialsId: 'e88eb18b-d1a9-4437-8ee8-7c83a66adb87', path: '', url: 'http://localhost:8001/')], contextPath: 'tasks', war: 'target/tasks.war'
                }
            }
        }
        stage('Frontend test'){
            steps{
                dir('tasks-web-test') {
                       git credentialsId: 'github_login', url: 'https://github.com/CarvalhoQA/tasks-web-test'
                       bat 'mvn test'
                }
           }
        }
    } 
}
