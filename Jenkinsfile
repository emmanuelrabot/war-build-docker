node {
    def app
    def registryProject='localhost:5000/wartest'
    def IMAGE="${registryProject}:version-${env.BUILD_ID}"

    stage ('Clone') {
        checkout scm
    }

    stage ('Maven package') {
        sh 'mvn package'
    }

    stage ('Build image') {
        app = docker.build("$IMAGE", '.')
    }

    stage ('Run image') {
        docker.image("${IMAGE}").withRun('--name run-$BUILD_ID -p 49152:8080') { c ->
            sh 'docker ps'
            sh 'netstat -ntaup'
            sh 'sleep 30s'
            sh 'curl http://localhost:49152/'
            sh 'docker ps'
       }
    }

    stage ('Push') {
        docker.withRegistry('http://localhost:5000') {
            app.push 'latest'
            app.push "$IMAGE"
        }
    }
}