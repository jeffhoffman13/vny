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
            passwordVariable: 'TRjsh12369',
            usernameVariable: 'trjsh'
        ]]
    ) {
        sh "docker login -u '${env.trjsh}' -p '${env.TRjsh12369}' -e demo@mesosphere.com"
        sh "docker push trjsh/vny:${gitCommit()}"
    }
    // Deploy
    stage 'Deploy'

    marathon(
        url: 'http://marathon.mesos:8080',
        forceUpdate: false,
        credentialsId: 'dcos-token',
        filename: 'marathon.json',
        appid: 'nginx-dcos',
        docker: "jsh123/vny:${gitCommit()}".toString()
    )
}
