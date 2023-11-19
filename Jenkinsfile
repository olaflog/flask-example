node {
    stage('Clone repository') {
        checkout scm
    }

    stage('Build image') {
        // Docker 이미지 빌드
        app = docker.build("fiscicdlab/flask-example")
    }

    stage('Push image') {
        docker.withRegistry('https://ec2-13-124-102-170.ap-northeast-2.compute.amazonaws.com/', 'harbor-reg') {
            // 이미지를 빌드 번호와 "latest" 태그로 푸시
            app.push("${env.BUILD_NUMBER}")
            app.push("latest")

            // deployment.yaml 파일 업데이트
            updateDeploymentYaml("${env.BUILD_NUMBER}")
        }
    }
}

def updateDeploymentYaml(buildNumber) {
    // deployment.yaml 파일 경로
    def deploymentYamlPath = "https://github.com/olaflog/flask-example-apps/blob/main/flask-example-deploy/deployment.yaml"

    // 파일 읽기
    def yamlContent = readFile(deploymentYamlPath)

    // 이미지 빌드 번호 업데이트
    yamlContent = yamlContent.replaceAll(/image: ec2-13-124-102-170.ap-northeast-2.compute.amazonaws.com\/fiscicdlab\/flask-example:.*/, "image: ec2-13-124-102-170.ap-northeast-2.compute.amazonaws.com/fiscicdlab/flask-example:${buildNumber}")

    // 업데이트된 내용으로 파일 쓰기
    writeFile file: deploymentYamlPath, text: yamlContent
}
