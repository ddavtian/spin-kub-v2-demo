pipeline {
    
    options {
      // Build auto timeout
      timeout(time: 60, unit: 'MINUTES')
    }
    
    agent { node { label 'kube-agent' } }
    
    // Pipeline stages
    stages {
        stage ('checkout from scm') {
          container('docker') {
            steps {
                echo "Checking out code"
                git branch: "master",
                    credentialsId: 'jenkins-ssh-key',
                    url: 'git@github.com:ddavtian/spin-kub-v2-demo.git'
            }
          }
        }
    }
}