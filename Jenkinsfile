@Library('sharedlib@podstemplate') _
stage("Build : Docker"){
    String podLabel = "${env.JOB_NAME}-${UUID.randomUUID().toString()[-3..-1]}".replace("/", "-")
    podTemplate(label: podLabel , yaml : libraryResource('Docker.yaml')) { 
        timestamps {
            node(podLabel) {
                checkout scm
                withAWS(credentials: 'gameKey', region: 'ap-southeast-1') {
                ecrLogin()
                container('docker'){
                    def accountIdentity = awsIdentity()
                    def IMAGE_REPO_NAME = "XXXXXX"
                    sh "docker pull nginx"
                    sh "docker images"
                    }
                }
            }
        }
    }          
}