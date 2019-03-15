node {

    checkout scm

    env.DOCKER_API_VERSION="1.23"
    
    sh "git rev-parse --short HEAD > commit-id"

    tag = readFile('commit-id').replace("\n", "").replace("\r", "")
    appName = "hello-python"
    registryHost = "127.0.0.1:30400/"
    imageName = "${registryHost}${appName}:${tag}"
    env.BUILDIMG=imageName

    stage "Build"
        sh "docker build -t ${imageName} ."
    stage "Push"
        sh "docker push ${imageName}"
    stage "Deploy"
        cd /yaml_gen
        sh "sed 's#__IMAGE__#'$BUILDIMG'#' python-deploy.yaml"
        cd ..
        kubernetesDeploy configs: "/yaml_gen/*.yaml , kubeconfigId: 'minikube'"
        // configs: "python-deploy.yaml, kubeconfigId: 'kubeconfig'"
        //sh "sed 's#__IMAGE__#'$BUILDIMG'#' python-deploy.yaml | kubectl apply -f -"
}
        
