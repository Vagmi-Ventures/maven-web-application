

node{
    
    //properties([buildDiscarder(logRotator(artifactDaysToKeepStr: '', artifactNumToKeepStr: '', daysToKeepStr: '5', numToKeepStr: '')), pipelineTriggers([cron('* * * * *')])])

    try{
        def mavenHome = tool name: "Maven3.9.10"
        stage('CheckoutCode'){
            git credentialsId: 'd7bef15c-d6cc-45db-92e8-2712d5a7f3db', url: 'https://github.com/Vagmi-Ventures/maven-web-application.git'
        }
        
        stage('Build'){
            sh "${mavenHome}/bin/mvn clean package"
        }
        
        stage('Sonarqube'){
            sh "${mavenHome}/bin/mvn sonar:sonar"
        }
        
       stage('DeploymentToServer'){
           sshagent(['d2d229b8-af64-4477-a48a-441f6164e580']) {
           sh "scp -o StrictHostKeyChecking=no target/maven-web-application.war ec2-user@172.31.6.208:/opt/apache-tomcat-9.0.106/webapps/"     
          }
       }
    } catch (e) {
    // If there was an exception thrown, the build failed
    currentBuild.result = "FAILURE"
    throw e
  } finally {
    // Success or failure, always send notifications
    notifyBuild(currentBuild.result)
  }
}

//slack to send the notification

def notifyBuild(String buildStatus = 'STARTED') {
  // build status of null means successful
  buildStatus =  buildStatus ?: 'SUCCESSFUL'

  // Default values
  def colorName = 'RED'
  def colorCode = '#FF0000'
  def subject = "${buildStatus}: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]'"
  def summary = "${subject} (${env.BUILD_URL})"

  // Override default values based on build status
  if (buildStatus == 'STARTED') {
    color = 'YELLOW'
    colorCode = '#FFFF00'
  } else if (buildStatus == 'SUCCESSFUL') {
    color = 'GREEN'
    colorCode = '#00FF00'
  } else {
    color = 'RED'
    colorCode = '#FF0000'
  }

  // Send notifications
  slackSend (color: colorCode, message: summary)
}
