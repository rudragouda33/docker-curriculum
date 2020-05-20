pipeline {
    agent any
    environment {
        registry = "rudragoudadockerhub/dockerdemo"
        GOCACHE = "/tmp"
    }
    stages {
        stage('Build') {
            environment {
                registryCredential = 'dockerhub'
            }
            steps{
                script {
                    dir("flask-app"){
                    def appimage = docker.build registry + ":$BUILD_NUMBER"
                    
                    docker.withRegistry( '', registryCredential ) {
                        appimage.push()
                        appimage.push('latest')
                    }
                    }
                }
            }
        }
        stage ('Deploy') {
            steps {
                 sshagent(['ssh-pipelinedemo']) {
                  
                     script{
                        def image_id = registry + ":$BUILD_NUMBER"
                        
                        try{   
                          sh "ssh ec2-user@3.88.35.101 docker pull $image_id"
                        }catch(error){
                         sh "ssh ec2-user@3.88.35.101 docker run -p 8888:5000 $image_id"
                        }
                     }   
                 }
            }
        }
    }
}
