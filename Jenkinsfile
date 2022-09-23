@Library('sharedlib@podstemplate') _
node("linux-01")
{
    switch(env.BRANCH_NAME) {

    case "develop":

        echo "hello"

        break

    case "main":

        stage("Build : Docker"){

        String podLabel = "${env.JOB_NAME}-${UUID.randomUUID().toString()[-3..-1]}".replace("/", "-")

        podTemplate(label: podLabel , yaml : libraryResource('Docker.yaml')) {

            timestamps {

                node(podLabel) {

                    checkout scm

                    container('docker'){

                    sh "docker pull nginx"
                    sh "docker images"

                    }

                }

            }

        }          

    }

        break

}
}
