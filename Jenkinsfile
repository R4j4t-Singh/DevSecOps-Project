pipeline {
    agent {
        label 'jenkins-agent'
    }

    tools {
        jdk 'jdk17'
        nodejs 'node16'
    }

     environment {
        SCANNER_HOME=tool 'sonar-scanner'
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

        stage("Quality Gate") {
            steps {
                waitForQualityGate abortPipeline: false, credentialsId: 'sonar-token'
            }
        }

        stage("Install Dependencies") {
            steps{
                sh "npm install"
            }
        }

        stage('OWASP FS SCAN') {
            steps {
                dependencyCheck additionalArguments: '--scan ./ --disableYarnAudit --disableNodeAudit', odcInstallation: 'DP-Check'
                dependencyCheckPublisher pattern: 'dependency-check-report.xml'
            }
        }

    }
    
}