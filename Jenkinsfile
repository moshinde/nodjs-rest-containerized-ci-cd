def label = "pod-${env.JOB_NAME}-${env.BUILD_NUMBER}".replace('_', '-').replace('/', '-')
podTemplate(label: label, containers: [
    containerTemplate(name: 'docker', image: 'docker', command: 'cat', ttyEnabled: true)],
    volumes: [hostPathVolume(hostPath: '/var/run/docker.sock', mountPath: '/var/run/docker.sock')]
  ) {
    node(label) {
	parameters {
        	tgstring(name: 'PORT', defaultValue: "30000", description: 'look for the free port')
    	}
        stage('Build Docker Image'){
            container('docker') {
                git 'https://github.com/moshinde/nodjs-rest-containerized-ci-cd.git'
                script {
                    sh 'whoami'
                    sh 'ls /home/jenkins/agent/workspace'
                    nodejs_image=docker.build("monicashinde3/nodjs-rest-containerized-ci-cd:${env.BUILD_ID}")                    
                }
            }
        }

        stage('Push to Docker hub'){
            container('docker') {
                script {
                    withDockerRegistry([ credentialsId: "docker-hub-user", url: "" ]){
                        nodejs_image.push()
                    }
                }
            }
        }

        stage('Deploy in container'){
            container('docker') {
                script{
                    if (env.BRANCH_NAME == 'master'){
                        env.ENV="prod"
                    }else{
                        env.ENV="dev"
                    }
                    env.PORT="${params.PORT}"
                    kubernetesDeploy configs: 'deploy.cd.yml',
                            kubeConfig: [path: ''],
                            kubeconfigId: 'jenkins-service-acct-kubeconfig',
                            enableConfigSubstitution: true
                }
            }
        }
    }

}
