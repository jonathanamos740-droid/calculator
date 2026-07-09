pipeline {
    agent any
    tools {
        maven 'Maven'
    }
    stages {
        stage('Checkout') {
            steps {
                git url: 'https://github.com/jonathanamos740-droid/calculator.git',
                    branch: 'main'
            }
        }
        stage('Compile') {
            steps {
                bat 'mvn clean compile'
            }
        }
        stage('Unit Test') {
            steps {
                bat 'mvn test'
            }
            post {
                always {
                    junit 'target/surefire-reports/*.xml'
                }
            }
        }
        stage('Code coverage') {
            steps {
                bat 'mvn clean verify'
                publishHTML(target: [
                    reportDir: 'target/site/jacoco',
                    reportFiles: 'index.html',
                    reportName: 'JaCoCo Report',
                    keepAll: true,
                    alwaysLinkToLastBuild: true,
                    allowMissing: false
                ])
            }
        }
    }
}