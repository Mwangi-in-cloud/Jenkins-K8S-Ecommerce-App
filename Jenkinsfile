pipeline {
    agent any 
    tools {
        jdk 'jdk17'
        maven 'maven'
    }
    environment {
        SCANNER_HOME = tool 'sonar-scanner'
        DOCKER_IMAGE = "cronosm4m/seller"
        TAG = "${env.BUILD_NUMBER}"
    }
    stages {
        stage ('checking out repo') {
            steps {
               git 'https://github.com/Mwangi-in-cloud/Jenkins-K8S-Ecommerce-App.git' 
            }
        }
        stage ('maven compile') {
            steps {
                sh "mvn compile"
            }
        }
        stage ('maven test') {
            steps  {
                sh "mvn test -DskipTests=true"
            }
        }
        stage ('trivy file scan') {
            steps {
                sh "trivy fs --format table -o trivy-fs-report.html ."
            }
        }
        stage ("now to sonar") {
            steps {
                    withSonarQubeEnv('sonar') {
                    sh '''
                    $SCANNER_HOME/bin/sonar-scanner \
                    -Dsonar.projectName=ECommerce \
                    -Dsonar.projectKey=ECommerce \
                    -Dsonar.java.binaries=target/classes
                    '''
                } 
            }
        }
        stage ("now building with maven") {
            steps {
                 sh "mvn package -DskipTests=true"
            }
        }
        stage ('publishing to nexus') {
            steps {
               withMaven(globalMavenSettingsConfig: 'maven-setting', jdk: 'jdk17', maven: 'maven') {
                    sh "mvn deploy -DskipTests=true"
                }  
            }
        }
        stage ("now docker building") {
            steps {
                sh 'docker build -t "$DOCKER_IMAGE:$TAG" .'
            }
        }
        stage ("now to hub") {
            steps {
                withCredentials([usernamePassword(credentialsId: 'docker-creds', passwordVariable: 'DOCKERHUB_PWD', usernameVariable: 'DOCKERHUB_USER')]) {
               sh 'echo "$DOCKERHUB_PWD" | docker login -u "$DOCKERHUB_USER" --password-stdin'
                sh 'docker push "$DOCKER_IMAGE:$TAG"'
               }
            }
        }
    }
}