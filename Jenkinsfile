def registry = "https://sameerr.jfrog.io"
def imageName = 'valaxy05.jfrog.io/valaxy-docker-local/ttrend'
def version   = '2.1.4'
pipeline {
  agent {
    node { label 'maven' }
  }
  environment{PATH = "/opt/apache-maven-3.9.5/bin:$PATH"}

  stages {
    stage("build") {
      steps { sh 'mvn clean deploy -Dmaven.test.skip=true'
       }
    }
    stage("test"){
    steps{
        echo "----------- unit test started ----------"
        sh 'mvn surefire-report:report'
            echo "----------- unit test Complted ----------"
    }
        }
    stage('SonarQube analysis') {
      environment{scannerHome = tool 'sonar-scanner'}
    steps{
      withSonarQubeEnv(
          'sonarqube-server') {  // If you have configured more than one global
                                 // server connection, you can specify its name
        sh "${scannerHome}/bin/sonar-scanner"
      }
    }
  }
    stage("Quality Gate"){
    steps {
        script {
        timeout(time: 1, unit: 'HOURS') { // Just in case something goes wrong, pipeline will be killed after a timeout
    def qg = waitForQualityGate() // Reuse taskId previously collected by withSonarQubeEnv
    if (qg.status != 'OK') {
      error "Pipeline aborted due to quality gate failure: ${qg.status}"
    }
  }
}
    }
  }
}
Credentials : artfiact-cred
      
         stage("Jar Publish") {
        steps {
            script {
                    echo '<--------------- Jar Publish Started --------------->'
                     def server = Artifactory.newServer url:registry+"/artifactory" ,  credentialsId:"artfiact-cred"
                     def properties = "buildid=${env.BUILD_ID},commitid=${GIT_COMMIT}";
                     def uploadSpec = """{
                          "files": [
                            {
                              "pattern": "jarstaging/(*)",
                              "target": "libs-release-local/{1}",
                              "flat": "false",
                              "props" : "${properties}",
                              "exclusions": [ "*.sha1", "*.md5"]
                            }
                         ]
                     }"""
                     def buildInfo = server.upload(uploadSpec)
                     buildInfo.env.collect()
                     server.publishBuildInfo(buildInfo)
                     echo '<--------------- Jar Publish Ended --------------->'  
            
            }
        }   
    }
}