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
               withAWS(credentials: 'gameKey', region: 'ap-southeast-1') {
                   ecrLogin()
                    // sh """
                    //     /kaniko/executor --dockerfile `pwd`/Dockerfile --context `pwd` --destination=${accountIdentity.account}.dkr.ecr.${AWS_DEFAULT_REGION}.amazonaws.com/${IMAGE_REPO_NAME}:${BUILD_NUMBER}
                    // """ 
                    script { 
                        def accountIdentity = awsIdentity()
                        def IMAGE_REPO_NAME = "demo"
                        sh """
                            /kaniko/executor --dockerfile `pwd`/Dockerfile --context `pwd` --destination=${accountIdentity.account}.dkr.ecr.${AWS_DEFAULT_REGION}.amazonaws.com/${IMAGE_REPO_NAME}:${BUILD_NUMBER}
                        """     
                    }
                                  
                }

        }
      }
    }
  }
}

podTemplate(containers: [
    containerTemplate(name: 'awscli-kubectl-helm', image: 'kennywong137/awscli-kubectl-helm', ttyEnabled: true, command: 'cat')
  ]) {

    node(POD_LABEL) {
        stage('AWS') {
            checkout scm
            sh "ls"
            container('awscli-kubectl-helm') {
                stage('push EKS') {
                    withAWS(credentials: 'gameKey', region: 'ap-southeast-1') {
                    script { 
                        def accountIdentity = awsIdentity()
                        def IMAGE_REPO_NAME = "demo"
                        //def AWS_DEFAULT_REGION = "ap-southeast-1"
                        echo IMAGE_REPO_NAME
                        def helmOptions = "--set-string \"image.repository=${IMAGE_REPO_NAME},image.tag=${BUILD_NUMBER}\" --atomic --cleanup-on-fail"
                        sh "aws eks --region ap-southeast-1 update-kubeconfig --name cluster-01"
                        sh "helm upgrade -i test --namespace myweb -f helm_values/dev.yaml  $helmOptions simple-generic-eks"   
                        // kubectl apply -f eks-myweb-deploy.yml
                        // kubectl get all -n myweb
                    }
                }
                }

    }
        }
    }
  }
