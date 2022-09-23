switch(env.BRANCH_NAME) {

    case "develop":

        echo "hello"

        break

    case "main":

        checkout scm
        
        echo "${env.BUILD_NUMBER}"
        echo "<images-repo>:<tag>"
        echo "<images-repo>:${env.BUILD_NUMBER}"

        break

}