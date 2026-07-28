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
                subject: "${currentBuild.currentResult == 'SUCCESS' ? '✅' : '❌'} ${env.JOB_NAME} #${env.BUILD_NUMBER} - ${currentBuild.currentResult}",
                body: """
<html>
<body style="font-family: 'Segoe UI', Arial, sans-serif; background-color: #f4f5f7; margin: 0; padding: 24px;">
    <div style="max-width: 560px; margin: 0 auto; background: #ffffff; border-radius: 8px; overflow: hidden; box-shadow: 0 1px 3px rgba(0,0,0,0.1);">

        <div style="background-color: ${currentBuild.currentResult == 'SUCCESS' ? '#1a7f37' : '#cf222e'}; padding: 20px 24px;">
            <span style="color: #ffffff; font-size: 20px; font-weight: 600;">
                ${currentBuild.currentResult == 'SUCCESS' ? '✅ Build Successful' : '❌ Build Failed'}
            </span>
        </div>

        <div style="padding: 24px;">
            <table style="width: 100%; border-collapse: collapse; font-size: 14px;">
                <tr>
                    <td style="padding: 10px 0; color: #6e7781; width: 120px;">Project</td>
                    <td style="padding: 10px 0; color: #24292f; font-weight: 600;">${env.JOB_NAME}</td>
                </tr>
                <tr style="border-top: 1px solid #eaeef2;">
                    <td style="padding: 10px 0; color: #6e7781;">Build</td>
                    <td style="padding: 10px 0; color: #24292f;">#${env.BUILD_NUMBER}</td>
                </tr>
                <tr style="border-top: 1px solid #eaeef2;">
                    <td style="padding: 10px 0; color: #6e7781;">Status</td>
                    <td style="padding: 10px 0;">
                        <span style="background-color: ${currentBuild.currentResult == 'SUCCESS' ? '#dafbe1' : '#ffebe9'}; color: ${currentBuild.currentResult == 'SUCCESS' ? '#1a7f37' : '#cf222e'}; padding: 3px 10px; border-radius: 12px; font-size: 12px; font-weight: 600;">
                            ${currentBuild.currentResult}
                        </span>
                    </td>
                </tr>
                <tr style="border-top: 1px solid #eaeef2;">
                    <td style="padding: 10px 0; color: #6e7781;">Duration</td>
                    <td style="padding: 10px 0; color: #24292f;">${currentBuild.durationString}</td>
                </tr>
                <tr style="border-top: 1px solid #eaeef2;">
                    <td style="padding: 10px 0; color: #6e7781;">Branch</td>
                    <td style="padding: 10px 0; color: #24292f;">main</td>
                </tr>
            </table>

            <div style="margin-top: 24px; text-align: center;">
                <a href="${env.BUILD_URL}" style="display: inline-block; background-color: #24292f; color: #ffffff; text-decoration: none; padding: 10px 20px; border-radius: 6px; font-size: 14px; font-weight: 600;">
                    View Build Details
                </a>
            </div>
        </div>

        <div style="background-color: #f6f8fa; padding: 14px 24px; text-align: center; font-size: 12px; color: #6e7781;">
            Sent automatically by Jenkins CI/CD
        </div>
    </div>
</body>
</html>
"""
            )

            withCredentials([string(credentialsId: 'slack-webhook-url', variable: 'SLACK_URL')]) {
                bat """
                curl -X POST -H "Content-Type: application/json" ^
                -d "{\\"message\\": \\"Build ${currentBuild.currentResult}: ${env.JOB_NAME} #${env.BUILD_NUMBER}\\"}" ^
                %SLACK_URL%
                """
            }
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