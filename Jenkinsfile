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

    }

}

       





































    