node {
	def app

    checkout scm

    env.DOCKER_API_VERSION="1.23"
    
    sh "git rev-parse --short HEAD > commit-id"

    tag = readFile('commit-id').replace("\n", "").replace("\r", "")
    appName = "hello-kenzan"
    registryHost = "127.0.0.1:30400/"
    imageName = "${registryHost}${appName}"
    tagName = "${imageName}:${tag}"
    env.BUILDIMG=imageName

    stage "Build"
    
        sh "docker build -t ${imageName} -f applications/hello-kenzan/Dockerfile applications/hello-kenzan"
    stage "Tag"
	sh "docker tag ${imageName} ${tagName}"
    
    stage "Push"
    	sh "docker push ${imageName}"

    stage "Prep for Deployment"
	sh "kubectl delete service ${appName}"
	sh "kubectl delete deployment ${appName}"

    stage "Deploy"

        kubernetesDeploy configs: "applications/${appName}/k8s/*.yaml", kubeconfigId: 'kenzan_kubeconfig'

}
