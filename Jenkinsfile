pipeline {
  node('label'){
    //now you are on slave labeled with 'label'
    def workspace = /var/lib/jenkins/workspace/Datafeed_Pipeline  
    //${workspace} will now contain an absolute path to job workspace on slave 
  }
  agent any 
  stages {
    stage('Checkout Scm') {
      steps {
        checkout([$class: 'GitSCM', branches: [[name: '*/main']], doGenerateSubmoduleConfigurations: false, extensions: [], submoduleCfg: [], userRemoteConfigs: [[credentialsId: '2d203f30-8573-4625-ad33-33d5a2748d9f', url: 'https://github.com/agrosshans/datafeed-pipeline.git']]])
      }
    }

    stage('Building RPM') {
      steps {
        sh (returnStdout: true, script: '''
        if [ -d /var/lib/jenkins/workspace/Datafeed_Pipeline ]; then
          cd /var/lib/jenkins/workspace/Datafeed_Pipeline
          /bin/rpmbuild --sign --define '_topdir /var/lib/jenkins/workspace/Datafeed_Pipeline' -ba -vv SPECS/datafeed.spec
        fi
        '''.stripIndent())
      }
    }

  }
  post {
    always {
      echo 'No converter for Publisher: jenkins.plugins.rhnpush.RhnPush'
    }

  }
}
