pipeline {
    agent {
        label 'jenkins-agent'
    }

    tools {
        jdk 'jdk17'
        nodejs 'node16'
    }

    stages {
        stage("Cleanup workspace"){
            steps{
                cleanWs()
            }
        }

        stage("Checkout from SCM"){
            steps{
                git branch: 'main', url: 'https://github.com/R4j4t-Singh/DevSecOps-Project'
            }
        }

        stage("SonarQube Analysis") {
            steps{
                script{
                    withSonarQubeEnv(credentialsId: 'sonar-token') {
                        sh ''' $SCANNER_HOME/bin/sonar-scanner -Dsonar.projectName=Netflix \
                    -Dsonar.projectKey=Netflix '''
                    }
                }
            }
        }
    }
    
}