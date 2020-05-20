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
                    def appimage = docker.build registry + ":$BUILD_NUMBER"
                    
                    docker.withRegistry( '', registryCredential ) {
                        appimage.push()
                        appimage.push('latest')
                    }
                }
            }
        }
        stage ('Deploy') {
            steps {
                 sshagent(['ssh-pipelinedemo']) {
                  
                     script{
                        def image_id = registry + ":$BUILD_NUMBER"
                        sh "sed -i 's#image_id#${image_id}#g' deployment.yml"
                        sh "scp -o StrictHostKeyChecking=no deployment.yml service.yml admin@3.88.35.101:/home/admin"
                        try{   
                          sh "ssh admin@3.88.35.101 kubectl apply -f ."
                        }catch(error){
                         sh "ssh admin@3.88.35.101 kubectl create -f ."
                        }
                     }   
                 }
            }
        }
    }
}
