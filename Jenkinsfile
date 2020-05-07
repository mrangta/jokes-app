pipeline {
    
    environment {
        repository = "mrangta010/jokes-app-new"
        credentials = 'dockerhub'
        dockerImage = ''
        PASSWORD = ${param.DOCKER_CONTENT_TRUST_REPOSITORY_PASSPHRASE}
    }

    tools { 
        maven 'maven3.6.3' 
        jdk 'openjdk8' 
    }

    agent any
        
    stages {

        stage('Build Application') {
            steps {
                bat 'mvn clean install'
            }
        }

        stage('Build Image') {
            steps {
                script {
                    // build a docker image and tag it with the build number
                    dockerImage = docker.build("mrangta010/jokes-app-new:${env.BUILD_ID}")
                    
                }
            }
        }    

        stage('Sign Tag') {

            steps {   
                bat 'set DOCKER_CONTENT_TRUST_REPOSITORY_PASSPHRASE=$PASSWORD'      
                echo %DOCKER_CONTENT_TRUST_REPOSITORY_PASSPHRASE%
                bat 'docker trust sign mrangta010/jokes-app-new:${env.BUILD_ID}'
                bat 'docker trust sign mrangta010/jokes-app-new:latest'
            }
        }

        stage('Deploy Image') {
            steps {
                script {
                    // push the docker image built in the previous step
                    docker.withRegistry('', credentials) {
                        dockerImage.push()
                        dockerImage.push('latest')
                    }
                } 
            }
        }

        stage('cleanup') {
            steps {
                /*
                    Delete the image pushed to registry in the previous step
                */
               bat "docker rmi $repository:$BUILD_NUMBER"     
            }
        }
    }
}