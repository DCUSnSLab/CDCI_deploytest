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
        app = docker.build("harbor.cu.ac.kr/cdcitest/nodejsdeploy")
        sh """
        docker run --name sample -d -u root harbor.cu.ac.kr/cdcitest/nodejsdeploy
        docker cp sample:/app/dist .
        docker rm -f sample
        """
        sh "ls -al"
        sh "ls -al dist"
        sh """
        tar cvf dist.tar ./dist/
        """
    }

    stage('SSH transfer') {
        steps([$class: 'BapSshPromotionPublisherPlugin']) {
            sshPublisher(
                continueOnError: false, failOnError: true,
                publishers: [
                    sshPublisherDesc(
                        configName: "dcusnslab",//Jenkins 시스템 정보에 사전 입력한 서버 ID
                        verbose: true,
                        transfers: [
                            sshTransfer(
                                sourceFiles: "dist.tar", //전송할 파일
                                removePrefix: "", //파일에서 삭제할 경로가 있다면 작성
                                remoteDirectory: "/static_files", //배포할 위치
                                execCommand: "ls -al /static_files" //원격지에서 실행할 커맨드
                            )
                        ]
                    )
                ]
            )
        }
    }

    stage('Complete') {
        sh "echo 'The end'"
    }
  }
