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
            recordCoverage(tools: [[parser: 'JUNIT', pattern: 'server/target/surefire-reports/**/*.xml']])
            jacoco()
        }
        }
        stage("UPLOAD ARTIFACT"){
            steps{
            nexusArtifactUploader artifacts: [[artifactId: 'webapp', classifier: '', file: 'webapp/target/webapp.war', type: 'war']], credentialsId: 'nexus-credentials1', groupId: 'com.example.maven-project', nexusUrl: '15.207.84.156:8081', nexusVersion: 'nexus3', protocol: 'http', repository: 'capstone-project-snapshot-repo', version: '1.0-SNAPSHOT'
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
        stage("EXECUTE ANSIBLE PLAYBOOK FOR KUBERNETES DEPLOYMENT"){
           steps{
           ansiblePlaybook disableHostKeyChecking: true, installation: 'ansible', inventory: 'hosts', playbook: 'playbook.yml'
           }
       }
    }
}
