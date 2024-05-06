pipeline {

    agent any

	tools {
        go 'go1.22.1'
    }
    
    environment {
        registry = "afeez511/http-ehco"
        registryCredential = "dockerhub"  
        BIN_NAME = "http-echo"
        // TARGETOS = "linux"
        // TARGETARCH = "amd64"
        // DOCKER_BUILD_PATH =  "/dist/$TARGETOS/$TARGETARCH/$BIN_NAME"
    }
    
    stages {

        stage('Build') {
            steps {
                sh "go build -o $BIN_NAME ."
                // sh "mkdir -p $DOCKER_BUILD_PATH"
                // sh "cp -r * $DOCKER_BUILD_PATH"
            }
        }

        stage('Build DockerApp Image') {
          steps {
            script {
              dockerImage = docker.build registry + ":V$BUILD_NUMBER" // , "--build-arg BIN_NAME=${BIN_NAME}", "--build-arg DOCKER_BUILD_PATH=${DOCKER_BUILD_PATH}"
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

        stage('App deployment') {
         // agent {label 'KOPS'}
            steps {
              sh "helm upgrade --install --force ${BIN_NAME} helm/http-echo --set appimage=${registry}:V${BUILD_NUMBER}"
            }
        }
        
        stage('Prometheus via helm') {
          steps{
            sh "helm repo add prometheus-community https://prometheus-community.github.io/helm-charts"
            sh "helm repo update"
            sh "helm install prometheus prometheus-community/prometheus"
          }
        }

        stage('Grafana via helm') {
          steps{
            sh "helm repo add grafana https://grafana.github.io/helm-charts"
            sh "helm repo update"
            sh "helm install grafana grafana/grafana"
          }

        }

        stage("Temporary Service expose") {
          steps {     
              sh "kubectl expose service prometheus-server --type=NodePort --target-port=9090 --name=prometheus-server-ext"
              sh "kubectl expose service grafana --type=NodePort --target-port=3000 --name=grafana-ext"              
            }

        }

        // stage("Ingress-controller via Helm") {
        //     steps {
        //         script {
        //             dir('lets-Encrpt') {
        //                 sh "helm repo add nginx-stable https://helm.nginx.com/stable"
        //                 sh "helm repo update"
        //                 sh "helm install nginx-ingress nginx-stable/nginx-ingress --set rbac.create=true --values values.yaml"
        //             }
        //         }
        //     }
        // }
    }

}

       





































    