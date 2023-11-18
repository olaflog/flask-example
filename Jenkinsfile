node {
     stage('Clone repository') {
         checkout scm
     }

     stage('Build image') {
         app = docker.build("730135569722.dkr.ecr.ap-northeast-2.amazonaws.com/fiscdlab")
     }

     stage('Push image') {
         docker.withRegistry('https://730135569722.dkr.ecr.ap-northeast-2.amazonaws.com', 'ecr:ap-northeast-2:jenkins-ecr-cred') {
             app.push("${env.BUILD_NUMBER}")
             app.push("latest")
     }
  }
}
