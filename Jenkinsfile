pipeline{
 agent CI_env
   tools {
     maven 'm3'
   }
   environment {
      def server = Artifactory.newServer url: 'http://10.5.0.12:8081/artifactory/', username: 'admin', password: 'password'
      buildInfo = Artifactory.newBuildInfo()
      def workspace = pwd()
      def tomcat_path = '/opt/tomcat/webapps/ROOT/'

      // Servers
      slave = '10.5.0.14'
      QA_server = '10.5.0.15'
      artifactory_server = 'http://10.5.0.12:8081/artifactory/'
   }
 stages{
    stage('checkout SCM'){
        steps {
            checkout scm
        }
    }
    stage ("initialize") {
      steps {
        sh '''
            echo "PATH = ${PATH}"
            echo "M2_HOME = ${M2_HOME}"
            '''
        }
    }

    stage('SonarQube analysis') {
       when { not { branch 'master'}}
       steps {
         sh "echo OK"
       }
    }
    stage (' Build '){
      steps{
       dir( 'app' ) {
           script {
                def server = Artifactory.newServer url: 'http://10.5.0.12:8081/artifactory/', username: 'admin', password: 'password'
                def rtMaven = Artifactory.newMavenBuild()
                rtMaven.resolver server: server, releaseRepo: example-repo-local, snapshotRepo: example-repo-local
                rtMaven.deployer server: server, releaseRepo: example-repo-local, snapshotRepo: example-repo-local
                rtMaven.tool = 'm3'
                def buildInfo = rtMaven.run pom: 'pom.xml', goals: 'install'
                buildInfo.env.capture = true
                server.publishBuildInfo buildInfo
                def pom = readMavenPom file: 'pom.xml'
                id = pom.artifactId
                version = pom.version
           }
       }
    }
   }
 }
}
