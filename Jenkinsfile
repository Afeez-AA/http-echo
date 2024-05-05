pipeline {

    agent any

	tools {
        go 'go1.22.1'
    }
    
    environment {
        registry = "afeez511/http-ehco"
        registryCredential = "dockerhub"  
        BIN_NAME = "http-echo"
    
    }
    
    stages {

        stage('Build') {
            steps {
                sh "go build -o $BIN_NAME ."
            }
        }

        stage('Build DockerApp Image') {
          steps {
            script {
              dockerImage = docker.build registry + ":V$BUILD_NUMBER" 
            }
          }
        }

        stage('Upload Image'){
          steps{
            script {
              docker.withRegistry('', registryCredential) {
                dockerImage.push("V$BUILD_NUMBER")
                dockerImage.push('latest')
              }
            }
          }
        }

        stage('Remove Unused docker image') {
          steps{
            sh "docker rmi $registry:V$BUILD_NUMBER"
          }
        }

        stage('Kubernetes Deploy') {
            steps {
              sh "helm upgrade --install --force ${BIN_NAME} helm/http-echo --set appimage=${registry}:V${BUILD_NUMBER}"
            }
        }


    }

}

       





































    