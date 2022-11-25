pipeline{
    agent any
    tools{
        maven "mvn3.8.6"
    }
    stages{
        stage('Checkout Code'){
            steps{
                  sendslacknotifications("STARTED")
                git credentialsId: 'githubcredentials', url: 'https://github.com/sudheerchinta1/maven-web-application.git'
                 }
        }
        stage('Build'){
            steps{
                 sendslacknotifications("BUILDING")
             sh "mvn clean package"   
            }
      
      }
      stage('ExecuteSonarQubeReport'){
            steps{
              sendslacknotifications("SONARTESTING")        
             sh "mvn clean sonar:sonar"   
            }
      
      }
      stage('UploadingIntoNexus'){
            steps{
             sh "mvn clean deploy"   
            }
      
      }
      stage('DeployingIntoTomcat'){
            steps{
             sshagent(['tomcatcredentialsforSSH']) {
             sh "scp -o StrictHostKeyChecking=no target/maven-web-application.war ec2-user@172.31.84.198:/opt/apache-tomcat-9.0.68/webapps/"
             }
      }
    }
  }
   post{
      aborted{
              sendslacknotifications(currentBuild.result)
             }
      success{
              sendslacknotifications(currentBuild.result)
              }
      failure{
              sendslacknotifications(currentBuild.result)
             }
     }
}

def sendslacknotifications(String buildStatus = 'STARTED') {
  // build status of null means successful
  buildStatus =  buildStatus ?: 'SUCCESS'

  // Default values
  def colorName = 'RED'
  def colorCode = '#FF0000'
  def subject = "${buildStatus}: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]'"
  def summary = "${subject} (${env.BUILD_URL})"

  // Override default values based on build status
  if (buildStatus == 'STARTED') {
    color = 'YELLOW'
    colorCode = '#FFFF00'
  } else if (buildStatus == 'SUCCESS') {
    color = 'GREEN'
    colorCode = '#00FF00'
  } 
    else if (buildStatus == 'BUILDING') {
    color = 'PINK'
    colorCode = '#FF69B4'
    }
    else if (buildStatus == 'SONARTESTING') {
    color = 'INDIGO'
    colorCode = '#4B0082'
    }
    else {
    color = 'RED'
    colorCode = '#FF0000'

  }

  // Send notifications
  slackSend (color: colorCode, message: summary, channel: "kmartdev")
}
