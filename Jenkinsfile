pipeline {
	agent any
    parameters {
        string(name: 'PORT', defaultValue: "30000", description: 'look for the free port')
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
                    if (env.BRANCH_NAME == 'master'){
                        env.ENV="prod"
                    }else{
                        env.ENV="dev"
                    }
                    env.PORT="${params.PORT}"
                    kubernetesDeploy configs: 'deploy.cd.yml',
                            kubeConfig: [path: ''],
                            kubeconfigId: 'k8s-jenkins',
                            enableConfigSubstitution: true
                }
            }
        }
    }
    
}