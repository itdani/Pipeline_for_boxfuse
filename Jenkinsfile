pipeline {
  agent {

    docker {
      image '35.192.124.146:8123/boxfuse:1.0.0'
    }

  }

  stages {

    stage('Copy source with configs') {
      steps {
        git 'https://github.com/itdani/boxfuse3.git'
      }
    }

    stage('Build war') {
      steps {
        sh 'mvn package'
        nexusArtifactUploader artifacts: [[artifactId: 'hello-1.0',
        classifier: 'debug', file: '/var/lib/jenkins/workspace/boxfuse/target/hello-1.0.war', type: 'war']],
        credentialsId: 'pas-nexus', groupId: 'sc', nexusUrl: '35.192.124.146:8081', nexusVersion: 'nexus3',
        protocol: 'http', repository: 'maven-releases', version: '1.0.1'
      }
    }

    stage('Deploy') {
      steps {
        sshPublisher(publishers: [sshPublisherDesc(configName: 'prod_serv',
        transfers: [sshTransfer(cleanRemote: false, excludes: '',
        execCommand: 'for container_id in $(docker ps  --filter="name=$name" -q);do docker stop $container_id && docker rm $container_id;done && \
        for container_id in $(docker ps  --filter="name=$name" -q -a);do docker rm $container_id;done && \
        docker pull 35.192.124.146:8123/prodimage:1.0.1 && \
        docker run -d -p 8080:8080 -v /usr/local/tomcat/webapps:/usr/local/tomcat/webapps/ 35.192.124.146:8123/prodimage:1.0.1',
        execTimeout: 120000, flatten: false, makeEmptyDirs: false, noDefaultExcludes: false,
        patternSeparator: '[, ]+', remoteDirectory: '', remoteDirectorySDF: false,
        removePrefix: 'target', sourceFiles: 'target/*.war')],
        usePromotionTimestamp: false, useWorkspaceInPromotion: false, verbose: false)])
      }
    }
  }
}
