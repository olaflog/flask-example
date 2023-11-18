node {
     stage('Clone repository') {
         checkout scm
     }
     stage('Build image') {
         app = docker.build("admin/flask-example")
         
     }
     stage('Push image') {
         docker.withRegistry('https://730135569722.dkr.ecr.ap-northeast-2.amazonaws.com/flask-example-apps/', 'ecr:ap-northeast-2:jenkins-ecr-cred') {
             app.push("${env.BUILD_NUMBER}")
             app.push("latest")
         }
     }
}
