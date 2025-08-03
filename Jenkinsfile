pipeline {
    agent any

    tools {
        jdk 'java-21'
        maven 'maven'
    }

    environment {
        SONAR_HOME = tool 'sonar-scanner'
    }

    stages {
        stage('Git Checkout') {
            steps {
                git branch: 'main', changelog: false, poll: false, url: 'https://github.com/jaiswaladi246/Ekart.git'
            }
        }

        stage('Maven Compile') {
            steps {
                sh 'mvn clean compile -DskipTests=true'
            }
        }

        stage('SonarQube Scanning') {
            steps {
                withSonarQubeEnv('sonar-scanner') {
                    sh '''
                        ${SONAR_HOME}/bin/sonar-scanner \
                        -Dsonar.projectKey=eshopping \
                        -Dsonar.projectName=eshopping \
                        -Dsonar.sources=. \
                        -Dsonar.java.binaries=target/classes
                    '''
                }
            }
        }
        stage('Maven Packaging') {
            steps {
                sh 'mvn clean package -DskipTests=true'
            }
        }
        stage('Docker build') {
            steps {
                sh 'docker build -t eshopping:latest -f docker/Dockerfile .'
            }
        }
         stage('Docker Run') {
    steps {
        sh '''
            docker rm -f eshopping || true
            docker run -itd --name eshopping -p 8070:8070 eshopping:latest
        '''
    }
}

    }
}


