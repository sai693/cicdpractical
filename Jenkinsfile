pipeline {
    agent any
    tools {
        jdk 'jdk11'
        maven 'maven3'
    }
    environment {
        SCANNER_HOME = tool 'sonar-scanner'
    }

    stages {
        stage('Git Checkout') {
            steps {
                git(
                    branch: 'master',
                    changelog: false,
                    poll: false,
                    url: 'https://github.com/sai693/cicdpractical.git'
                )
            }
        }
        stage('Compile') {
            steps {
                sh 'mvn clean package -DskipTests=true'
            }
        }
        stage('OWASP Scan') {
            steps {
                dependencyCheck(
                    additionalArguments: '--scan ./',
                    odcInstallation: 'DP'
                )
                dependencyCheckPublisher(
                    pattern: '**/dependency-check-report.xml'
                )
            }
        }
        stage('SonarQube') {
            steps {
                withSonarQubeEnv('sonar-server') {
                    sh '''$SCANNER_HOME/bin/sonar-scanner \
                        -Dsonar.projectName=cicdpractical \
                        -Dsonar.java.binaries=. \
                        -Dsonar.projectKey=cicdpractical'''
                }
            }
        }
        stage('Build') {
            steps {
                sh 'mvn clean package -DskipTests=true'
            }
        }
        stage('Docker Build and Push') {
            steps {
                script {
                    // Build and Tag the Docker Image in One Command
                    sh "docker build -t sai693/cicdpractical:latest -f docker/Dockerfile ."

                    // Push the Docker Image to Docker Hub
                    sh "docker push sai693/cicdpractical:latest"
                }
            }
        }
        //stage('Deploy Docker Container') {
            //steps {
              //  script {
                    // Run the Docker Container with port mapping
                 //   sh "docker run -d -p 8070:8070 sai693/cicdpractical:latest"
               // }
           // }
       // }
    }
}
