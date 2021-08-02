pipeline {
  agent {
    node {
      label 'GOL'
    }

  }
  stages {
    stage('BUILD') {
      agent {
        node {
          label 'GOL'
        }

      }
      steps {
        build 'mvn package'
      }
    }

    stage('Artifactory') {
      steps {
        archiveArtifacts '**/gameoflife.war'
      }
    }

  }
}