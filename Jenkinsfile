pipeline {
    agent any
    environment {
        registry = "658408998517.dkr.ecr.us-east-2.amazonaws.com/mypythonpapp"
        //- update your credentials ID after creating credentials for connecting to Docker Hub
        registryCredential = '658408998517'
        dockerImage = ''
    }
   
    stages {
        stage('Cloning Git') {
            steps {
                checkout([$class: 'GitSCM', branches: [[name: '*/master']], doGenerateSubmoduleConfigurations: false, extensions: [], submoduleCfg: [], userRemoteConfigs: [[credentialsId: '', url: 'git clone https://github.com/femilv/dockerapp.git ']]])      
            }
        }
   
    // Building Docker images
    stage('Building image') {
      steps{
        script {
          dockerImage = docker.build registry
            docker.build('mypythonpapp')
        }
      }
    }
    
     // Uploading Docker images into Docker Hub
    stage('Upload Image to ECR') {
     steps{   
         script {
            docker.withRegistry( '658408998517.dkr.ecr.us-east-2.amazonaws.com', "ecr:us-east-2:$registryCredential" ) {
            docker.image("mypythonpapp"). push('latest')
            }
        }
      }
    }
   
     // Stopping Docker containers for cleaner Docker run
     stage('stop previous containers') {
         steps {
            sh 'docker ps -f name=mypythonContainer -q | xargs --no-run-if-empty docker container stop'
            sh 'docker container ls -a -fname=mypythonContainer -q | xargs -r docker container rm'
         }
       }
     
    // Running Docker container, make sure port 8096 is opened in
    stage('Docker Run') {
     steps{
         script {
            dockerImage.run("-p 8096:5000 --rm --name mypythonContainer")
         }
      }
    }
  }
}
