node{
    checkout scm
    env.DOCKER_API_VERSION="1.23"

    sh "git rev-parse --short HEAD > commit-id"
    tag = readFile('commit-id').replace("\n", "").replace("\r", "")
    registry_host = env.registry_host

    // build
    stage("build: JenkinsTest"){
        sh "docker build --tag ${registry_host}/JenkinsTest:${tag} --file JenkinsTest/Dockerfile ."
    }

    // push
    stage("push"){
        sh "docker push ${registry_host}/JenkinsTest:${tag}"
    }

    // deploy
    stage("deploy"){
        sh "sed -i 's|{{tag}}|${tag}|g' JenkinsTest/deployment.yaml \
        && sed -i 's|{{registry_host}}|${registry_host}|g' JenkinsTest/deployment.yaml \
        && kubectl ${env.token_kube} apply -f JenkinsTest/deployment.yaml"
    }
}