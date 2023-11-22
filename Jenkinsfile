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
        APP_NAME = "netflix-clone"
        RELEASE = "1.0.0"
        DOCKER_USER = "rajat202011061"
        DOCKER_PASS = 'docker'
        IMAGE_NAME = "${DOCKER_USER}" + "/" + "${APP_NAME}"
        IMAGE_TAG = "${RELEASE}" + "-" + "${BUILD_NUMBER}"
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
                dependencyCheckPublisher pattern: '**/dependency-check-report.xml'
            }
        }

        stage("Build and Push Docker Image") {
            steps {
                script {
                    docker.withRegistry('', DOCKER_PASS) {
                        docker_image = docker.build("${IMAGE_NAME}")
                    }
                    docker.withRegistry('', DOCKER_PASS) {
                        docker_image.push("${IMAGE_TAG}")
                        docker_image.push("latest")
                    }
                }
            }
        }

    }
    
}