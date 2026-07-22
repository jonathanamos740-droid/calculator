pipeline {
    agent any


    triggers {
        githubPush()
        cron('H 2 * * *')
    }
    post {
        always {
            emailext(
                mimeType: 'text/html',
                to: 'peternatt6@gmail.com',
                subject: "Build ${currentBuild.currentResult}: ${env.JOB_NAME} #${env.BUILD_NUMBER}",
                body: """
    <html>
    <h2>Jenkins Build Notification</h2>
    <table border="1" cellpadding="8">
    <tr><td><b>Project</b></td><td>${env.JOB_NAME}</td></tr>
    <tr><td><b>Build</b></td><td>${env.BUILD_NUMBER}</td></tr>
    <tr><td><b>Status</b></td><td>${currentBuild.currentResult}</td></tr>
    <tr><td><b>Duration</b></td><td>${currentBuild.durationString}</td></tr>
    </table>
    <br><a href="${env.BUILD_URL}">Open Build</a>
    </html>
    """
            )
        }
    }

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
        stage('Checkstyle') {
            steps {
                bat 'mvn checkstyle:checkstyle'
                publishHTML(target: [
                    reportDir: 'target/reports',
                    reportFiles: 'checkstyle.html',
                    reportName: 'Checkstyle Report',
                    keepAll: true,
                    alwaysLinkToLastBuild: true,
                    allowMissing: false
                ])
            }
        }
    }
}