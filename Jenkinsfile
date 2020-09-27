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
              docker.withRegistry("https://docker.e5labs.com", 'e5-labs-docker-repo') {
                docker.image("docker.e5labs.com/e5labs/spin-kub-v2-demo").build()
                docker.image.push()
            }
          }
        }
        
        stage ('build helm chart') {
          steps {
            container('helm') {
                echo "Building Helm Chart"
                sh "apk --no-cache add curl"
                sh "helm repo add e5-labs-helm https://nexus.e5labs.com/repository/helm-hosted/"
                sh "helm package chart"
                sh "curl https://nexus.e5labs.com/repository/helm-hosted/ --upload-file spin-kub-v2-demo-0.1.0.tgz -v"
            }
          }
        }
    }
}