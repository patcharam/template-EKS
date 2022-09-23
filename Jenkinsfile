@Library('sharedlib@podstemplate') _
stage("Build : Docker"){
    String podLabel = "${env.JOB_NAME}-${UUID.randomUUID().toString()[-3..-1]}".replace("/", "-")
    podTemplate(label: podLabel , yaml : libraryResource('Docker.yaml')) { 
        timestamps {
            node(podLabel) {
                checkout scm
            
                container('docker'){
                    
                    def IMAGE_REPO_NAME = "demo"
                    sh "docker pull nginx:latest"
                    sh "docker images"
                    
                    withAWS(credentials: 'gameKey', region: 'ap-southeast-1') {
                    ecrLogin()
                    def accountIdentity = awsIdentity()
                    sh "docker tag nginx:latest ${accountIdentity.account}.dkr.ecr.${AWS_DEFAULT_REGION}.amazonaws.com/${IMAGE_REPO_NAME}:${BUILD_NUMBER}"
                    sh "docker push ${accountIdentity.account}.dkr.ecr.${AWS_DEFAULT_REGION}.amazonaws.com/${IMAGE_REPO_NAME}:${BUILD_NUMBER}"
                    sh "docker images"
                    ecrListImages()
                    
                    }
                }
            }
        }
    }          
}