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
        pwd
        ls -al
        """
    }

    stage("Publish on SSH") {
            steps {
                sshPublisher(publishers: [sshPublisherDesc(configName: 'snslab_ssh', transfers: [sshTransfer(cleanRemote: false, excludes: '', execCommand: '', execTimeout: 120000, flatten: false, makeEmptyDirs: false, noDefaultExcludes: false, patternSeparator: '[, ]+', remoteDirectory: '/static_files', remoteDirectorySDF: false, removePrefix: '', sourceFiles: 'dist.tar')], usePromotionTimestamp: false, useWorkspaceInPromotion: false, verbose: true)])
            }
        }

    stage('Complete') {
        sh "echo 'The end'"
    }
  }
