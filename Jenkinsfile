//properties([pipelineTriggers([githubPush()])])
def label = "worker-${UUID.randomUUID().toString()}"
    environment { 
        BUILD = '20220224' 
    }
podTemplate(label: label, containers: [
  containerTemplate(name: 'dev', image: 'ngol/dev3:v1', command: 'cat', imagePullPolicy: 'Always' , privileged: true, ttyEnabled: true),
],
volumes: [
  hostPathVolume(mountPath: '/mnt', hostPath: '/mnt')
]) 

{
  node(label) {
       git url: "https://github.com/ngo-l/jenkins-sops-test",branch: 'master'

    stage('sops') {

        println(WORKSPACE)
        echo "build = ${BUILD}"
      }
    }


  }
}
