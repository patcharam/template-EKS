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
                    def IMAGE_REPO_NAME = "demo"
                    sh "docker pull nginx:latest"
                    sh "docker images"
                    sh "docker tag nginx:latest ${accountIdentity.account}.dkr.ecr.${AWS_DEFAULT_REGION}.amazonaws.com/${IMAGE_REPO_NAME}:${BUILD_NUMBER}"
                    sh "docker images"
                    sh "docker push ${accountIdentity.account}.dkr.ecr.${AWS_DEFAULT_REGION}.amazonaws.com/${IMAGE_REPO_NAME}:${BUILD_NUMBER}"
                    
                    ecrListImages()
                    
                    }
                }
            }
        }
    }          
}