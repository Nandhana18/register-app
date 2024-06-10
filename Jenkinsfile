pipeline{
    agent any
    stages{
        stage("CLONE THE PROJECT"){
            steps{
                git branch: 'main', url: 'https://github.com/Nandhana18/register-app.git'
            }
        }
        stage("MAVEN BUILD"){
            steps{
                sh "mvn clean package"
                sh "mvn clean verify site"
            }
        }
        stage("CODE REVIEW"){
            steps{
                withSonarQubeEnv('sonarqube') {
                sh "mvn sonar:sonar"
                }
            }
        }
        stage("UNIT TEST AND CODE COVERAGE"){
            steps{
    
            recordCoverage(tools: [[pattern: 'target/site/jacoco/*.html'], [parser: 'JUNIT', pattern: 'target/surefire-reports/**/*.xml']])
        }
        }
        stage("UPLOAD ARTIFACT"){
            steps{
            nexusArtifactUploader artifacts: [[artifactId: 'webapp', classifier: '', file: 'webapp/target/webapp.war', type: 'war']], credentialsId: 'nexus-credentials1', groupId: 'com.example.maven-project', nexusUrl: '13.200.252.40:8081', nexusVersion: 'nexus3', protocol: 'http', repository: 'capstone-project-snapshot-repo', version: '1.0-SNAPSHOT'
        }
        }
        stage("BUILD A DOCKER IMAGE"){
            steps{
                sh "docker build -t nandhanab/register-app ."
            }
        }
        stage("PUSH TO DOCKER HUB"){
            steps{
                withCredentials([usernamePassword(credentialsId: 'dockerhub', passwordVariable: 'dockerhubpwd', usernameVariable: 'dockerhubusername')]) {
                    sh "docker login -u nandhanab -p ${dockerhubpwd}"
                    sh "docker push nandhanab/register-app"
                }
            }
        }
    }
}
