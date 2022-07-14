node
{
    def mavenHome = tool name: "maven3.8.6"
    properties([buildDiscarder(logRotator(artifactDaysToKeepStr: '', artifactNumToKeepStr: '5', daysToKeepStr: '', numToKeepStr: '5')), [$class: 'JobLocalConfiguration', changeReasonComment: ''], pipelineTriggers([pollSCM('* * * * *')])])
echo "The job name is : ${env.JOB_NAME}"
echo "The build number is : ${env.BUILD_NUMBER}"
echo "The node name is : ${env.NODE_NAME}"
echo "The Build id is : ${env.BUILD_ID}"
echo "The build tag is : ${env.BUILD_TAG}"
    
stage('Getting code from GitHub'){
    git branch: 'development', url: 'https://github.com/jagadeeshgithub97/maven-web-application.git'
}
stage('Build'){
    sh "${mavenHome}/bin/mvn clean package"
}
stage('Execute sonarqube reort'){
    sh "${mavenHome}/bin/mvn sonar:sonar"
}  
stage('Upload artifacts into nexus'){
    sh "${mavenHome}/bin/mvn deploy"
}
stage('Deploy application into Tomcat'){
    sshagent(['421a0526-1287-42dc-8cf8-66438e2c817d']){
    sh " scp -o StrictHostKeyChecking=no target/maven-web-application.war ec2-user@52.66.199.107:/opt/apache-tomcat-9.0.64/webapps/"
    }
}     

catch(e){
currentBuild.result = "FAILURE"
throw e
}
finally{
slacknotifications(currentBuild.result)
}
    
}

def slacknotifications(String buildStatus = 'STARTED') {
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
