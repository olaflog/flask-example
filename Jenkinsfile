node {
     stage('Clone repository') {
         checkout scm
     }
     stage('Build image') {
         app = docker.build("fiscicdlab/flask-example")
         
     }
     stage('Push image') {
         docker.withRegistry('https://ec2-13-124-102-170.ap-northeast-2.compute.amazonaws.com/', 'harbor-reg') {
             app.push("${env.BUILD_NUMBER}")
             app.push("latest")
         }
     }
}
