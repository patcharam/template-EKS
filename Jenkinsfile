pipeline {
  agent {
    kubernetes {
      //cloud 'kubernetes'
      yaml """
kind: Pod
metadata:
  name: kaniko
spec:
  containers:
  - name: kaniko
    image: gcr.io/kaniko-project/executor:debug-539ddefcae3fd6b411a95982a830d987f4214251
    imagePullPolicy: Always
    command:
    - cat
    tty: true
    volumeMounts:
      - name: docker-config
        mountPath: /kaniko/.docker
  volumes:
    - name: docker-config
      configMap:
        name: docker-config
"""
    }
  }

  stages {
    stage('Build with Kaniko') {
      steps {
        git 'https://github.com/sd031/aws_codebuild_codedeploy_nodeJs_demo.git'
        container(name: 'kaniko') {
               withAWS(credentials: 'keyGame', region: 'ap-southeast-1') {
                   ecrLogin()
                    sh """
                        /kaniko/executor --dockerfile `pwd`/Dockerfile --context `pwd` --destination=${accountIdentity.account}.dkr.ecr.${AWS_DEFAULT_REGION}.amazonaws.com/${IMAGE_REPO_NAME}:${BUILD_NUMBER}
                    """ 
                    script { 
                        def accountIdentity = awsIdentity()
                        def IMAGE_REPO_NAME = "devops-gc-poc"
                        // sh """
                        //     /kaniko/executor --dockerfile `pwd`/Dockerfile --context `pwd` --destination=${accountIdentity.account}.dkr.ecr.${AWS_DEFAULT_REGION}.amazonaws.com/${IMAGE_REPO_NAME}:${BUILD_NUMBER}
                        // """     
                    }
                                  
                }

        }
      }
    }
  }
}
