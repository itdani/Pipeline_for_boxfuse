pipeline {
  agent {

    docker {
      image '34.70.29.145:8123/boxfuse:1.0.0'
    }

  }

  stages {

    stage('Copy source with configs') {
      steps {
        git 'https://github.com/itdani/boxfuse3.git'
      }
    }

    stage('Build war and push repo') {
      steps {
        sh 'mvn package'
        nexusArtifactUploader artifacts: [[artifactId: 'hello-1.0', classifier: 'debug', file: '/var/lib/jenkins/workspace/boxfuse/target/hello-1.0.war', type: 'war']], credentialsId: 'pas-nexus', groupId: 'sc', nexusUrl: '34.70.29.145:8081', nexusVersion: 'nexus3', protocol: 'http', repository: 'maven-releases', version: '1.0.1'
      }
    }

    stage('Deploy in serv_prod') {
      steps {
        sshPublisher(publishers: [sshPublisherDesc(configName: 'prod_serv', transfers: [sshTransfer(cleanRemote: false, excludes: '', execCommand: 'apt update && apt install -y docker.io && docker stop $(sudo docker ps -aq) && docker rm $(sudo docker ps -aq) && docker pull 34.70.29.145:8123/prodimage:1.0.1 && docker run -d -p 8080:8080 -v /usr/local/tomcat/webapps:/usr/local/tomcat/webapps/ 34.70.29.145:8123/prodimage:1.0.1', execTimeout: 120000, flatten: false, makeEmptyDirs: false, noDefaultExcludes: false, patternSeparator: '[, ]+', remoteDirectory: '', remoteDirectorySDF: false, removePrefix: 'target', sourceFiles: 'target/*.war')], usePromotionTimestamp: false, useWorkspaceInPromotion: false, verbose: false)])
      }
    }
  }
}
