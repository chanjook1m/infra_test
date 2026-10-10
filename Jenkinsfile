pipeline {
  agent { label 'ansible && rocky9' }

  options {
    skipDefaultCheckout(true)
  }

  triggers {
    pollSCM('H/2 * * * *')
  }

  stages {
    stage('Checkout') {
      steps {
        checkout scm

        sh '''
          set -eu
          echo "Node: $NODE_NAME"
          echo "Workspace: $WORKSPACE"
          echo "Commit:"
          git log -1 --oneline
          echo "Remote branch containing this commit:"
          git branch -r --contains HEAD
          test -f k8s/demo.yaml
        '''
      }
    }

    stage('CI: Verify agent') {
      steps {
        sh '''
          set -eu
          hostname
          java -version
          ansible --version
          ansible localhost -i 'localhost,' -c local \
            -m ansible.builtin.ping
        '''
      }
    }

    stage('CI: Check demo manifest') {
      steps {
        sh '''
          set -eu
          grep -q 'name: chargeops-demo' k8s/demo.yaml
          grep -q 'message:' k8s/demo.yaml
          echo "Manifest smoke check passed"
        '''
      }
    }
  }
}