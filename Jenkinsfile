pipeline {
    
    environment {
        repository = 'mrangta010/jokes-app'
        credentials = 'dockerhub'
        dockerImage = ''
    }

    tools { 
        maven 'Maven 3.3.9' 
        jdk 'jdk8' 
    }

    agent any
        
    stages {

        stage('Build Application') {
            steps {
                sh 'mvn clean install'
            }
        }

        stage('Build Image') {
            steps {
                script {
                    // build a docker image and tag it with the build number
                    dockerImage = docker.build repository + ":$BUILD_NUMBER" .
                }
            }
        }

        stage('Deploy Image') {
            steps {
                script {
                    // push the docker image built in the previous step
                    docker.withRegistry('', credentials)
                    dockerImage.push()
                } 
            }
        }

        stage('cleanup') {
            steps {
                /*
                    Delete the image pushed to registry in the previous step
                */
               sh "docker rmi $repository:$BUILD_NUMBER"     
            }
        }
    }
}