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
          steps {
                script {
                   // Check if Prometheus is already installed
                   def existingPrometheus = sh(script: 'helm list -q | grep -c prometheus', returnStdout: true).
                   if (existingPrometheus == '0') {
                       // Prometheus is not installed, deploy it
                       sh "helm upgrade --install --force prometheus prometheus-community/prometheus"
                   } else {
                       // Prometheus is already installed, skip deployment
                       echo "Prometheus is already installed, skipping deployment."
                   }
                }
          }
        }


        stage('Grafana via helm') {
          steps {
                script {
                    // Check if Grafana is already installed
                    def existingGrafana = sh(script: 'helm list -q | grep -c grafana', returnStdout: true).trim()

                    if (existingGrafana == '0') {
                        // Grafana is not installed, deploy it
                        sh "helm repo add grafana https://grafana.github.io/helm-charts"
                        sh "helm repo update"
                        sh "helm upgrade --install --force grafana grafana/grafana"
                    } else {
                        // Grafana is already installed, skip deployment
                        echo "Grafana is already installed, skipping deployment."
                    }
                }
          }
        }

        stage("Ingress-controller via Helm") {
            steps {
                script {
                    dir('lets-Encrpt') {
                        sh "helm repo add nginx-stable https://helm.nginx.com/stable"
                        sh "helm repo update"
                        sh "helm install nginx-ingress nginx-stable/nginx-ingress --set rbac.create=true --values values.yaml"
                    }
                }
            }
        }
    
    
    
    
    
    
    
    
    
    
    
    }

}

       




































    