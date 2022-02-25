properties([pipelineTriggers([githubPush()])])
def label = "worker-${UUID.randomUUID().toString()}"
//testing1

podTemplate(label: label, containers: [
  containerTemplate(name: 'buildah', image: 'ngol/buildah:v1', command: 'cat', imagePullPolicy: 'Always' , privileged: true, ttyEnabled: true),
  containerTemplate(name: 'sops', image: 'ngol/sops:v2', command: 'cat', imagePullPolicy: 'Always' , privileged: true, ttyEnabled: true),
],
volumes: [
  hostPathVolume(mountPath: '/mnt', hostPath: '/mnt')
]) 

{
  node(label) {
       git url: "https://github.com/ngo-l/jenkins-sops-test" ,branch: 'master'
       env.GIT = "git@github.com:ngo-l/jenkins-sops-test.git"
       env.ENCRYPT = "staging/secrets-enc.env"
       env.DECRYPT = "secrets.env"


  stage('Git source') {
        container('buildah') {        
            withCredentials([sshUserPrivateKey(credentialsId: "9bcc9b80-6899-4b4b-8aa5-eb0080545418", keyFileVariable: 'keyfile')]) {
              // start ssh-agent
              sh "mkdir ~/.ssh/"
              sh 'ssh-agent /bin/bash'           
              // add private key to ssh-agent, check if private key is successfully added and git clone using the private key
              sh 'eval $(ssh-agent) && ssh-add ${keyfile} && ssh-add -l && ssh-keyscan github.com  >> ~/.ssh/known_hosts && git clone ${GIT}'
              }
            sh 'pwd && ls ./'

        }
      }

    stage('sops'){
        container('sops') {
          sh "sops -v" 
          sh 'pwd && ls ./'        
           withCredentials([file(credentialsId: 'jenkins-sops-pvt-key', variable: 'FILE')]) {
              sh 'gpg --import $FILE'    
              sh "gpg --list-keys"
              sh "sops -d ${ENCRYPT} > ${DECRYPT}"                
            }
          sh "cat ${DECRYPT}"

        }
    }

  }
}

