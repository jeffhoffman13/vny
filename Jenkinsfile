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
            credentialsId: 'dockerhub-mesosphere',
            passwordVariable: 'docker69',
            usernameVariable: 'jsh123'
        ]]
    ) {
        sh "docker login -u '${env.jsh123}' -p '${env.docker69}' -e demo@mesosphere.com"
        sh "docker push jeffhoffman13/vny:${gitCommit()}"
    }
}
