/**
 * This pipeline will run a Docker image build
 */

def shOut(cmd){
  sh(script: cmd, returnStdout: true).trim()
}

withCredentials([usernamePassword(credentialsId: 'dockerhub', usernameVariable: 'USERNAME', passwordVariable: 'PASSWORD')]) {
  podTemplate(label: 'docker',
    containers: [
      containerTemplate(
        name: 'docker',
        image: 'docker:1.11',
        ttyEnabled: true,
          envVars: [
            containerEnvVar(key: 'USERNAME', value: env.USERNAME),
            containerEnvVar(key: 'PASSWORD', value: env.PASSWORD),
            containerEnvVar(key: 'DOCKER_PUSH', value: 'true')
            ],
        command: '/bin/cat')
       ],
    volumes: [
      hostPathVolume(
        hostPath: '/var/run/docker.sock',
        mountPath: '/var/run/docker.sock')
    ]
  ) {

    node('docker') {

      stage('Checkout'){
        git credentialsId: 'jenkins-ssh',
        url: 'git@github.com:tomski747/myrepo.git'
        buildMetadata = [
          'build_tag': env.BUILD_TAG,
          // 'build_tag': env.BUILD_TAG.replace('/','_'),
          'git.branch': shOut("git rev-parse --abbrev-ref HEAD"),
          'git.revision': shOut("git rev-parse HEAD")

        ]
        print buildMetadata
        // tags = []
        // labels = []
        // buildMetadata.each { k,v ->
        //   tags << ""
        //
        // }
      }
    }
  }
}
