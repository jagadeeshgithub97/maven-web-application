node
{
    def mavenHome = tool name: "maven3.8.5"
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
    sshagent(['421a0526-1287-42dc-8cf8-66438e2c817d'])
    { sh " scp -o StrictHostKeyChecking=no target/maven-web-application.war ec2-user@13.126.130.169:/opt/apache-tomcat-9.0.64/webapps/"
    }
    
}
}
