def gitCommit() {
    sh "git rev-parse HEAD > GIT_COMMIT"
    def gitCommit = readFile('GIT_COMMIT').trim()
    sh "rm -f GIT_COMMIT"
    return gitCommit
}

node {
    // Checkout source code from Git
    stage 'Checkout'
    checkout scm

    // Build Docker image
    stage 'Build'
    sh "docker build -t jeffhoffman13/vny:${gitCommit()} ."

    // Log in and push image to GitLab
    stage 'Publish'
    withCredentials(
        [[
            $class: 'UsernamePasswordMultiBinding',
            credentialsId: 'dockerhub',
            passwordVariable: 'docker69',
            usernameVariable: 'jsh123'
        ]]
    ) {
        sh "docker login -u '${env.jsh123}' -p '${env.docker69}' -e demo@mesosphere.com"
        sh "docker push hub.docker.com/r/jsh123/vny:${gitCommit()}"
    }
    // Deploy
    stage 'Deploy'

    marathon(
        url: 'http://marathon.mesos:8080',
        forceUpdate: false,
        credentialsId: 'dcos-token',
        filename: 'marathon.json',
        appid: 'nginx-mesos',
        docker: "hub.docker.com/r/jsh123/vny:${gitCommit()}".toString()
    )
}
