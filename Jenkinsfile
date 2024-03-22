pipeline {

    agent any

	tools {
        go 'go1.22.1'
    }
    
    // environment {
    //     registry = "afeez511/http-ehco"
    //     registryCredential = 'dockerhub'
    // }
    
    stages {

        stage('Build') {
            steps {
                sh 'go build -o main .'
            }
        }

        stage('Unit Test') {
            steps {
                script {
                    sh 'go mod init hello'
                    sh 'go test'
                }
            }
        }
    
        stage('Coverage Report') {
            steps {
                script {
                    sh 'go test -coverprofile=coverage.out'
                    sh 'go tool cover -html=coverage.out -o coverage.html'
                }
                archiveArtifacts 'coverage.html'
            }
        }

    }

}

       





































    