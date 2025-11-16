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
    }
}