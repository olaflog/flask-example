pipeline {
    agent any

    environment {
        IMAGE_REGISTRY_ACCOUNT = "ec2-13-124-102-170.ap-northeast-2.compute.amazonaws.com/fiscicdlab"
        IMAGE_NAME = "flask-example"
    }

    stages {
        stage('Clone repository') {
            steps {
                // 기존 코드: 소스 코드를 Jenkins에서 실행되는 에이전트(노드)에 체크아웃하는 단계
                checkout scm
            }
        }

        stage('Build image') {
            steps {
                // 기존 코드: Docker 이미지를 빌드하는 단계
                script {
                    app = docker.build("${IMAGE_REGISTRY_ACCOUNT}/${IMAGE_NAME}")
                }
            }
        }

        stage('Push image') {
            steps {
                // 기존 코드: Docker 이미지를 Harbor 저장소로 푸시하는 단계
                docker.withRegistry('https://ec2-13-124-102-170.ap-northeast-2.compute.amazonaws.com/', 'harbor-reg') {
                    app.push("${env.BUILD_NUMBER}")
                    app.push("latest")
                }
            }
        }

        stage('Update Kubernetes Manifests') {
            steps {
                // 새로운 코드: Jenkins에서 직접 실행되기 때문에 Git 체크아웃과 Manifests 업데이트를 동시에 수행
                sh '''
                    git clone -b main https://github.com/olaflog/flask-example-apps.git
                    cd flask-example-apps/flask-example-deploy
                    sed -i "s/image:.*/image: ${IMAGE_REGISTRY_ACCOUNT}\\/${IMAGE_NAME}:${BUILD_NUMBER}/g" deployment.yaml
                    git add deployment.yaml
                    git config --global user.name olaflog
                    git config --global user.email olaflog@elsa.anna
                    git commit -m "Jenkins Build Number - ${BUILD_NUMBER}"
                    git push origin main
                '''
            }
        }
    }

    post {
        success {
            echo 'Build and deployment successful!'
        }
        failure {
            echo 'Build or deployment failed!'
        }
    }
}
