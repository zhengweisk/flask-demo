node('haimaxy-jnlp') {
    stage('Clone') {
        echo "1.Clone 代码"
        git url: "https://github.com/yangmv/flask-demo.git"
        script {
            build_tag = sh(returnStdout: true, script: 'git rev-parse --short HEAD').trim()
        }
    }
    stage('Test') {
      echo "2.代码测试[PAAS]"
    }
    stage('Build') {
        echo "3.构建打包 Docker 镜像"
        sh "docker build -t yangmv/flask-demo:${build_tag} ."
    }
    stage('Push') {
        echo "4.推送 Docker 镜像到仓库"
        withCredentials([usernamePassword(credentialsId: 'dockerHub', passwordVariable: 'dockerHubPassword', usernameVariable: 'dockerHubUser')]) {
            sh "docker login -u ${dockerHubUser} -p ${dockerHubPassword}"
            sh "docker push yangmv/flask-demo:${build_tag}"
        }
    }
    stage('YAML') {
        echo "5.修改YAML版本"
        sh "sed -i 's/<BUILD_TAG>/${build_tag}/' flask-demo-deploy.yaml"
    }
    stage('Deploy') {
        echo "6.开始部署"
        def userInput = input(
            id: 'userInput',
            message: 'Choose a deploy environment',
            parameters: [
                [
                    $class: 'ChoiceParameterDefinition',
                    choices: "Dev\nQA\nProd",
                    name: 'Env'
                ]
            ]
        )
        echo "This is a deploy step to ${userInput}"
        if (userInput == "Dev") {
            // deploy dev stuff
        } else if (userInput == "QA"){
            // deploy qa stuff
        } else {
            // deploy prod stuff
        }
        sh "kubectl apply -f k8s.yaml"
    }
}