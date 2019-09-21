pipeline {
	agent any
    parameters {
        choice(name: 'ENVIRONMENT', choices: ["environment","dev", "prod"], description: 'Pick Environment')
    }
    stages{                
        stage('Build Docker Image'){
            steps{
                script {
                    echo "who am i?"
                    sh "whoami"
                        nodejs_image=docker.build("monicashinde3/nodjs-rest-containerized-ci-cd:${env.BUILD_ID}")                    
                }
            }
        }

        stage('Push to Docker hub'){
            steps{
                script {
                    withDockerRegistry([ credentialsId: "docker-hub-user", url: "" ]){
                        nodejs_image.push()
                    }
                }
            }
        }

        stage('Deploy in container'){
            steps{
                script{
                    env.ENV="${params.ENVIRONMENT}"
                    kubernetesDeploy configs: 'deploy.cd.yml',
                            kubeConfig: [path: ''],
                            kubeconfigId: 'k8s-jenkins',
                            enableConfigSubstitution: true
                }
            }
        }
    }
    
}