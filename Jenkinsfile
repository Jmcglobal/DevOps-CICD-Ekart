pipeline {
    agent any
    
    tools{
        // jdk 'jdk-11'
        maven 'maven3'
    }

    environment {
        SONAR_TOKEN = credentials('sonar')
        SONAR_URL = 'http://3.238.217.125:9000'
        SONAR_PROJECT_KEY ='sonarqube'
    }
    
    stages {
        stage('Git Clone') {
            steps {
                git branch: 'master', url: 'https://github.com/Jmcglobal/DevOps-CICD-Ekart'
            }
        }
        
        stage('mvn compile command') {
            steps {
                sh "mvn compile"
            }
        }
        
        stage('mvn test command') {
            steps {
                sh "mvn test"
            }
        }

        stage('Sonarqube Analysis') {
            steps {
                withSonarQubeEnv('sonarqube') {
                    sh '''mvn clean verify sonar:sonar \
                    -Dsonar.projectName=Petclinic \
                    -Dsonar.projectKey=Petclinic'''
                }
            }
        }

    // OR
        // stage('Sonarqube Analysis') {
        //     steps {
        //       sh '''mvn clean verify sonar:sonar \
        //         -Dsonar.projectKey=${SONAR_PROJECT_KEY} \
        //         -Dsonar.host.url=${SONAR_URL} \
        //         -Dsonar.login=${SONAR_TOKEN}'''
        //     }
        // }
        
        stage('mvn clean package') {
            steps {
                sh "mvn clean package"
            }
        }

        stage('Dependency-Check') {
            steps {
                dependencyCheck additionalArguments: '--scan target/', odcInstallation: 'owasp'
                dependencyCheckPublisher pattern: '**/dependency-check-report.xml'
            }
        }

        stage('DOCKER BUILDS AND PUSH') {
            steps {
                script{
                    withDockerRegistry(credentialsId: 'docker', toolName: 'docker') {
                        sh "docker build -t jmcglobal/shopping-cart -f docker/Dockerfile ."
                        sh " docker push jmcglobal/shopping-cart:latest"
                    }
                }
            }
        }

        stage('DOCKER RUN COMMAND') {
            steps {
                sh "docker run -d --rm --name cart -p8070:8070 jmcglobal/shopping-cart"
            }
        }
    }
}
