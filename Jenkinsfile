pipeline {
  agent any 
  stages {
    stage('Checkout Scm') {
      steps {
        checkout([$class: 'GitSCM', branches: [[name: '*/master']], doGenerateSubmoduleConfigurations: false, extensions: [], submoduleCfg: [], userRemoteConfigs: [[credentialsId: '2d203f30-8573-4625-ad33-33d5a2748d9f', url: 'https://github.com/agrosshans/datafeed-pipeline.git']]])
      }
    }

    stage('Building RPM') {
      steps {
        sh (returnStdout: true, script: '''
        if [ -d /var/lib/jenkins/workspace/Datafeed_Pipeline ]; then
          echo `pwd`
          cd /var/lib/jenkins/workspace/Datafeed_Pipeline
          /bin/rpmbuild --sign --define "_topdir /var/lib/jenkins/workspace/Datafeed_Pipeline" -ba -vv SPECS/datafeed.spec
        fi
        '''.stripIndent())
      }
    }
    
    stage('Pushing RPM') {
      steps {
        sh (returnStdout: true, script: '''
        if [ -d /var/lib/jenkins/workspace/Datafeed_Pipeline/RPMS/x86_64 ]; then
          cd /var/lib/jenkins/workspace/Datafeed_Pipeline/RPMS/x86_64
          find . -name "*rpm" | xargs rhnpush --channel=datafeed --server=http://spacewalk.lanathome.com/APP -v --tolerant -u datafeed -p datafeed
      fi
      '''.stripIndent())
      }
    }
    
    stage('Invoque Ansible Tower for datafeed package update') {
      steps {
        ansibleTower jobTemplate: 'deploy_datafeed', jobType: 'run', limit: 'docker04.lanathome.com', throwExceptionWhenFail: false, towerCredentialsId: '1117d6d6-3ac7-4181-8796-7ab2c9a8cee4', towerLogLevel: 'full', towerServer: 'awx01.lanathome.com', verbose: true
      }
    }
  }
  post {
    always {
      echo 'datafeed package is now available for install with yum update datafeed'
    }

  }
}