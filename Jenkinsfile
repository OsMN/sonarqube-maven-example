pipeline 
{
    /*
    agent 
    {
        docker 
        {
            image 'maven:3.9.4-eclipse-temurin-17-alpine' 
            args '-v /root/.m2:/root/.m2' 
        }
    }*/
    agent any
    tools
    {
        maven 'Maven 2.2.1'
        jdk 'JDK 6u45'
    }
    stages
    {
        stage('Build') 
        { 
            steps 
            {
                sh 'mvn -q clean package -Dmaven.test.skip=true'
            }
        }
        stage('Test') {
            steps {
                //sh 'mvn test'
                sh "mvn -q jacoco:prepare-agent test -fn jacoco:report"
            }
            post {
                always {
                    script {
                        try {
                            junit skipMarkingBuildUnstable: true, testResults:'**/target/surefire-reports/*.xml'
                            jacoco(execPattern: '**/target/jacoco.exec')
                        } catch (error) { 
                            echo "Error: ${error.getMessage()}"
                        }
                    }
                }
            }
        }
        stage('Sonarqube Analysis') {
            tools{jdk 'JDK 11'}
            environment { scannerHome = tool 'SonarScanner' }
            steps {
                withSonarQubeEnv('SonarQube') {
                    sh """
                        ${scannerHome}/bin/sonar-scanner \
                        -Dsonar.projectKey=test \
                        -Dsonar.projectName=test \
                        -Dsonar.host.url=http://localhost:9000/sonar \
                        -Dsonar.login=a34f07272d89e3d12c6b66c069e39e99d92e84b4 \
                        -Dsonar.language=java \
                        -Dsonar.java.binaries=**/classes \
                        -Dsonar.java.libraries=**/lib/*.jar \
                    """
                }
//           timeout(time: 2, unit: 'MINUTES') {
//                      script {
//                        waitForQualityGate abortPipeline: true
//                    }
//                }
            }
        }            
    }    
}
