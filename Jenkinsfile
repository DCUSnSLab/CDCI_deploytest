/* pipeline 변수 설정 */
def app

node {
    stage('Checkout') {
            checkout scm
    }

    // mvn 툴 선언하는 stage, 필자의 경우 maven 3.6.0을 사용중
    stage('Ready'){
        sh "echo 'Ready to build'"
    }

    //dockerfile기반 빌드하는 stage ,git소스 root에 dockerfile이 있어야한다
    stage('Build image'){
        sh "mkdir dist"
        sh "ls -al"
        app = docker.build("harbor.cu.ac.kr/cdcitest/nodejsdeploy")
        app.inside('-u root -v dist:/app'){}
        sh "ls -al"
        sh "ls -al dist"
    }

    stage('Complete') {
        sh "echo 'The end'"
    }
  }
