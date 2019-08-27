pipeline {
	agent any
    stages{                
        stage('Build Docker Image'){
            agent{ label 'jenkins-slave-1'}
            steps{
                script {
                    echo "who am i?"
                    sh "whoami"
                        exchange_image=docker.build("monicashinde3/nodjs-rest-containerized-ci-cd")                    
                }
            }
        }

        stage('Push to Docker hub'){
            steps{
                script {
                    withDockerRegistry([ credentialsId: "docker-hub-user", url: "" ]){
                        exchange_image.push()
                        conversion_image.push()
                    }
                }
            }
        }

        stage('Deploy in container'){
            steps{
                script{
                    sh 'docker-compose up -d'
                }
            }
        }
    }
    
}