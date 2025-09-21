node {

   echo "git branch name: ${env.JOB_NAME}"
   echo "build number is: ${env.BUILD_NUMBER}"
   echo "node name is: ${env.NODE_NAME}"

    def mavenpath= '/var/lib/jenkins/tools/hudson.tasks.Maven_MavenInstallation/Maven_3.9.11/bin'
  
    try {

    stage('git checkout'){
	notifyBuild('STARTED')
      git branch: 'dev', url: 'https://github.com/KKfundadevops6/maven-webapplication-project-kkfunda.git'
    }
    stage('compile'){
    sh  "${mavenpath}/mvn compile"
    }
    stage('Build') {
    sh  "${mavenpath}/mvn clean package"
    }
   stage('SQ Report'){
    sh  "${mavenpath}/mvn sonar:sonar"
    }
    stage('Deploy into Nexus'){
    sh  "${mavenpath}/mvn clean deploy"
    }
    stage('Deploy to Tomcat') {
        sh """
            curl -u pabloo:password \
  --upload-file "/var/lib/jenkins/workspace/jio-dev-scripted-pipeline/target/maven-web-application.war" \
  "http://3.27.82.238:8080/manager/text/deploy?path=/maven-web-application&update=true"
    """
    }

  } catch (e) {
    // If there was an exception thrown, the build failed
    currentBuild.result = "FAILED"
  } finally {
    // Success or failure, always send notifications
    notifyBuild(currentBuild.result)
  }
}

def notifyBuild(String buildStatus = 'STARTED') {
    buildStatus = buildStatus ?: 'SUCCESS'

    def colorCode = '#FF0000'  // default RED
    def subject = "${buildStatus}: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]'"
    def summary = "${subject} (${env.BUILD_URL})"

    if (buildStatus == 'STARTED') {
        colorCode = '#FFFF00'  // yellow
    } else if (buildStatus == 'SUCCESS') {
        colorCode = '#00FF00'  // green
    }
    slackSend(color: colorCode, message: summary, channel: '#jio-test')
}
