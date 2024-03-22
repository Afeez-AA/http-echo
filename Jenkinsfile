pipeline {

    agent any

	tools {
        go 'go1.22.1'
    }
    
    environment {
        registry = "afeez511/http-ehco"
        registryCredential = "dockerhub"   
    }
    
    stages {

        stage('Build') {
            steps {
                sh 'go build -o main .'
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

        // stage('Kubernetes Deploy') {
        //   agent {label 'KOPS'}
        //     steps {
        //       sh "helm upgrade --install --force vprofile-stack helm/vprofilecharts --set appimage=${registry}:V${BUILD_NUMBER} --namespace prod"
        //     }
        // }


    }

}

       





































    