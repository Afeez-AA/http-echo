pipeline {

    agent any

	tools {
        go 'go1.22.1'
    }
    
    environment {
        registry = "afeez511/http-ehco"
        registryCredential = "dockerhub"  
        BIN_NAME = "http-echo"
        DOCKER_BUILD_PATH =  "/dist/$TARGETOS/$TARGETARCH/$BIN_NAME"
    }
    
    stages {

        stage('Build') {
            steps {
                sh "go build -o $BIN_NAME ."
                sh "mkdir -p $DOCKER_BUILD_PATH"
                sh "cp -r * $DOCKER_BUILD_PATH"
            }
        }

        stage('Build DockerApp Image') {
          steps {
            script {
              dockerImage = docker.build registry + ":V$BUILD_NUMBER", "--build-arg BIN_NAME=${BIN_NAME}", "--build-arg DOCKER_BUILD_PATH=${DOCKER_BUILD_PATH}"
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

       





































    