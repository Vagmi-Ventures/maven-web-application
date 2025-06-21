

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
   // notifyBuild(currentBuild.result)
  }
}


