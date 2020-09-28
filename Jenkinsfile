pipeline {
    
    options {
      // Build auto timeout
      timeout(time: 60, unit: 'MINUTES')
    }

    environment {
      registry = "docker.e5labs.com/e5labs/spin-kub-v2-demo"
      dockerImage = ''
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

        stage('Building image') {
          steps {
            container('docker') {
              script {
                dockerImage = docker.build registry + ":$BUILD_NUMBER"
              }
            }
          }
        }

        stage('Deploy Image') {
          steps {
            container('docker') {
              script {
                docker.withRegistry('https://docker.e5labs.com', 'e5-labs-docker-repo') {
                dockerImage.push()
              }
            }
           }
          }
        }
        
        stage('Remove Unused docker image') {
          steps {
            container('docker') {
              sh "docker rmi $registry:$BUILD_NUMBER"
            }
          }
        }
        
        stage ('build helm chart') {
          environment {
            HELM_CREDS = credentials('e5-labs-helm-repo')
            version = ''
          }
          steps {
            container('helm') {
                echo "Building Helm Chart"
                sh "apk --no-cache add curl"
                sh "helm repo add e5-labs-helm https://nexus.e5labs.com/repository/helm-hosted/"
                sh "helm package chart"
                version = sh(script: "grep '0.1.1' chart/Chart.yaml | awk '{print $2}'", returnStdout: true).trim()
                sh "curl -u $HELM_CREDS https://nexus.e5labs.com/repository/helm-hosted/ --upload-file spin-kub-v2-demo-${version}.tgz -v"
            }
          }
        }
    }
}