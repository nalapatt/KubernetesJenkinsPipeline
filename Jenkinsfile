pipeline{
    agent any
    tools {
      maven 'mymaven'
    }
    environment {
      DOCKER_TAG = 2.0
    }
    stages{
        stage('SCM'){
            steps{
                git credentialsId: 'github', 
                    url: 'https://github.com/nalapatt/dockeransiblejenkins'
            }
        }
        
        stage('Maven Build'){
            steps{
                sh "mvn clean package"
            }
        }
        
        stage('Docker Build'){
            steps{
                sh "docker build . -t nalapatt123/hariapp:${DOCKER_TAG} "
            }
        }
        
        stage('DockerHub Push'){
            steps{
                withCredentials([string(credentialsId: 'docker-hub', variable: 'dockerHubPwd')]) {
                    sh "docker login -u nalapatt123 -p ${dockerHubPwd}"
                }
                
                sh "docker push nalapatt123/hariapp:${DOCKER_TAG} "
            }
        }
     
        stage('Docker Deploy'){
            steps{
                sshagent(['dev_slave']) {
                    sh 'ssh -o StrictHostKeyChecking=no deploy@172.31.31.200 "docker stop rabbitmq && docker rm --force my-app"'
                     sh 'ssh -o StrictHostKeyChecking=no deploy@172.31.31.200 "docker run -p 8080:8090 -d --name my-app nalapatt123/hariapp:${DOCKER_TAG}"'
                }
                  }
        }
    }
}