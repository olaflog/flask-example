pipeline {
    agent any

    environment {
        GITHUB_CREDENTIALS = credentials('github-jenkins') // 'github-credentials-id'에는 위에서 생성한 자격 증명 ID를 입력합니다.
        IMAGE_REGISTRY_ACCOUNT = "ec2-13-124-102-170.ap-northeast-2.compute.amazonaws.com/fiscicdlab"
        IMAGE_NAME = "flask-example"
        IMAGE_TAG = "r20231120-002"

    }

    stages {
        stage('Clone repository') {
            steps {
                checkout scm
            }
        }

        stage('Build image') {
            steps {
                script {
                       app = docker.build("${IMAGE_REGISTRY_ACCOUNT}/${IMAGE_NAME}")
                    // app 변수를 이 스크립트 내에서 사용 가능하도록 수정
                }
            }
        }

        stage('Push image') {
            steps {
                script {
                    docker.withRegistry('https://ec2-13-124-102-170.ap-northeast-2.compute.amazonaws.com/', 'harbor-reg') {
                        // 'app' 변수를 이 스크립트 내에서 사용 가능하도록 수정
                        app.push("${IMAGE_TAG}")
                        app.push("latest")
                    }
                }
            }
        }

        stage('Update Kubernetes Manifests') {
            steps {
                script {
                    withCredentials([usernamePassword(credentialsId: 'github-jenkins', passwordVariable: 'GITHUB_TOKEN', usernameVariable: 'GITHUB_USERNAME')]) {
                        sh '''
                            rm -rf flask-example-apps
                            git clone -b main https://github.com/olaflog/flask-example-apps.git
                            cd flask-example-apps/flask-example-deploy
                            sed -i 's|image:.*|image: ${IMAGE_REGISTRY_ACCOUNT}/${IMAGE_NAME}:${IMAGE_TAG}|g' deployment.yaml
                            git add deployment.yaml
                            git config --global user.name olaflog
                            git config --global user.email olaflog@elsa.anna
                            git commit -m 'Jenkins Build Number - ${IMAGE_TAG}'
                            git push https://${GITHUB_USERNAME}:${GITHUB_TOKEN}@github.com/olaflog/flask-example-apps.git
                        '''
                }
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
