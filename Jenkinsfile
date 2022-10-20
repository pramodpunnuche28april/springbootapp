
node {
stage("Git Clone"){

        git credentialsId: 'GIT_HUB_CREDENTIALS', url: 'https://github.com/pramodpunnuche28april/springbootapp.git'
    }
      stage('Gradle Build') {

       sh './gradlew build'

    }
    stage("Docker build"){
        sh 'docker version'
        sh 'docker build -t springboot-docker-demo .'
        sh 'docker image list'
        sh 'docker tag springboot-docker-demo pramodpunnuche28april/my-hub:springboot-docker-demo'
    }

    withCredentials([string(credentialsId: 'DOCKER_HUB_PASSWORD', variable: 'PASSWORD')]) {
        sh 'docker login -u pramodpunnuche28april -p $PASSWORD'
    }
    stage("Push Image to Docker Hub"){
        sh 'docker push  pramodpunnuche28april/my-hub:springboot-docker-demo'
    }
    stage("SSH Into k8s Server") {
        def remote = [:]
        remote.name = 'worker-node'
        remote.host = '192.168.1.7'
        remote.user = 'root'
        remote.password = '$PASSWORD'
        remote.allowAnyHosts = true
        sshCommand remote: remote, command: "ls -lrt"

        stage('Put k8s-spring-boot-deployment.yml onto k8smaster') {
            sshPut remote: remote, from: 'k8s-spring-boot-deployment.yml', into: '.'
        }
        stage('Deploy spring boot') {
         sshCommand remote: remote, command: "kubectl apply -f k8s-spring-boot-deployment.yml"
        }
    }
}
