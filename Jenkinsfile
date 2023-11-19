pipeline {
    agent any

    environment {
        IMAGE_REGISTRY_ACCOUNT = "ec2-13-124-102-170.ap-northeast-2.compute.amazonaws.com/fiscicdlab"
        IMAGE_NAME = "flask-example"
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
                    def app = docker.build("${IMAGE_REGISTRY_ACCOUNT}/${IMAGE_NAME}")
                    // app 변수를 이 스크립트 내에서 사용 가능하도록 수정
                }
            }
        }

        stage('Push image') {
            steps {
                script {
                    docker.withRegistry('https://ec2-13-124-102-170.ap-northeast-2.compute.amazonaws.com/', 'harbor-reg') {
                        // 'app' 변수를 이 스크립트 내에서 사용 가능하도록 수정
                        def app = docker.image("${IMAGE_REGISTRY_ACCOUNT}/${IMAGE_NAME}:${env.BUILD_NUMBER}")
                        app.push()
                        app.push("latest")
                    }
                }
            }
        }

        stage('Update Kubernetes Manifests') {
            steps {
                script {
                    // 'app' 변수를 이 스크립트 내에서 사용 가능하도록 수정
                    def app = docker.image("${IMAGE_REGISTRY_ACCOUNT}/${IMAGE_NAME}:${env.BUILD_NUMBER}")
                    sh '''
                        git clone -b main https://github.com/olaflog/flask-example-apps.git
                        cd flask-example-apps/flask-example-deploy
                        sed -i "s/image:.*/image: ${IMAGE_REGISTRY_ACCOUNT}\\/${IMAGE_NAME}:${env.BUILD_NUMBER}/g" deployment.yaml
                        git add deployment.yaml
                        git config --global user.name olaflog
                        git config --global user.email olaflog@elsa.anna
                        git commit -m "Jenkins Build Number - ${env.BUILD_NUMBER}"
                        git push origin main
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
