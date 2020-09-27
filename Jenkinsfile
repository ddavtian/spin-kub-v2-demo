pipeline {
    
    options {
      // Build auto timeout
      timeout(time: 60, unit: 'MINUTES')
    }
    
    agent { node { label 'kube-agent' } }
    
    // Pipeline stages
    stages {
        
        stage ('checkout from scm') {
          steps {
            container('docker') {
              echo "Checking out code"
              git branch: "master",
                  credentialsId: 'jenkins-ssh-key',
                  url: 'git@github.com:ddavtian/spin-kub-v2-demo.git'
            }
          }
        }
        
        stage ('build docker image') {
          steps {
            container('docker') {
              echo "Building Application"
              sh "docker version"
              sh "docker build -t docker.e5labs.com/e5labs/spin-kub-v2-demo ."
            
              withDockerRegistry(credentialsId: 'e5-labs-docker-repo', url: 'https://docker.e5labs.com') {
                sh "docker push docker.e5labs.com/e5labs/spin-kub-v2-demo"
              }
            }
          }
        }
        
        stage ('build helm chart') {
          steps {
            container('helm') {
                echo "Building Helm Chart"
                sh "helm package /chart"
            }
          }
        }
    }
}