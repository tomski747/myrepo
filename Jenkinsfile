/**
 * This pipeline will run a Docker image build
 */

def shOut(cmd){
  sh(script: cmd, returnStdout: true).trim()
}

def dockerTagJoin(tg){
  params = []
  tg.each{
    params << "--tag ${prefix}:${it}"
  }
  params.join(' ').replace('/','_')
}

def dockerLabelJoin(lbl){
  params = []
  lbl.each{ k,v ->
    params << "--label ${k}=${v}"
  }
  params.join(' ')
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
        branchName = env.BRANCH_NAME
        buildTag = "${branchName}-${env.BUILD_NUMBER}"
        registryPrefix = 'geoquant/myrepo'
        buildMetadata = [
          'build_tag':  buildTag,
          'git.branch': branchName,
          'git.revision': shOut("git rev-parse HEAD")

        ]
        tags = dockerTagJoin([branchName,buildTag])
        labels = dockerLabelJoin(buildMetadata)
        cmd =  "echo docker build ${tags} ${labels} ."
        sh cmd
      }
    }
  }
}
