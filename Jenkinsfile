pipeline {
    agent any

 parameters {
        choice(name: 'account', choices:  ['dev', 'qa', 'stage', 'prod'], description: 'Select the environment.')
        string(name: 'commit_id', defaultValue: 'latest', description: 'provide commit id.')
    }
    
    stages {
        stage('Docker Image Build IN Dev') {
             when {
                expression {
                    params.account == 'dev'
                }
            }
             script {
             def app = docker.build("armansk9129/dev-jenkins:latest")
             docker.withRegistry('https://registry.hub.docker.com/armansk9129/dev-jenkins', 'DOCKER/DEV') {
                app.push()
              }
            }
         }

        
        stage('Docker Image pull tag push to qa') {
            when {
                expression {
                    params.account == 'qa'
                }
            }
             //pulling an image 
             script {
             docker.withRegistry('https://registry.hub.docker.com/armansk9129/dev-jenkins', 'DOCKER/DEV') {
             docker.image("armansk9129/dev-jenkins:latest").pull()
             }
        }
             //tagging an image 
            script {
             sh 'echo Image pulled from DEV'
             sh 'echo Tagging Docker image from Dev to QA'
             sh "docker tag armansk9129/dev-jenkins:latest  armansk9129/qa-jenkins:latest" 
        
            //pushing an image 
            docker.withRegistry('https://registry.hub.docker.com/armansk9129 qa-jenkins', 'DOCKER/QA') {
            docker.image("armansk9129/qa-jenkins:latest").push()
            }
         }   
       
       
        stage('Docker Image pull tag push to stage') {
             when {
                expression {
                    params.account == 'stage'
                }
            }
            //pulling an image 
             script {
             docker.withRegistry('https://registry.hub.docker.com/armansk9129/qa-jenkins', 'DOCKER/QA') {
             docker.image("armansk9129/qa-jenkins:latest").pull()
             }
        }
             //tagging an image 
            script {
             sh 'echo Image pulled from DEV'
             sh 'echo Tagging Docker image from QA to STAGE'
             sh "docker tag armansk9129/qa-jenkins:latest  armansk9129/stage-jenkins:latest" 
        
            //pushing an image 
            docker.withRegistry('https://registry.hub.docker.com/armansk9129 stage-jenkins', 'DOCKER/STAGE') {
            docker.image("armansk9129/stage-jenkins:latest").push()
            }
         }   
        
        stage('Docker Image pull tag push to prod') {
           when {
                expression {
                    params.account == 'prod'
                }
            }
            //pulling an image 
             script {
             docker.withRegistry('https://registry.hub.docker.com/armansk9129/stage-jenkins', 'DOCKER/STAGE') {
             docker.image("armansk9129/stage-jenkins:latest").pull()
             }
        }
             //tagging an image 
            script {
             sh 'echo Image pulled from stage'
             sh 'echo Tagging Docker image from STAGE to PROD'
             sh "docker tag armansk9129/stage-jenkins:latest  armansk9129/prod-jenkins:latest" 
        
            //pushing an image 
            docker.withRegistry('https://registry.hub.docker.com/armansk9129 prod-jenkins', 'DOCKER/PROD') {
            docker.image("armansk9129/prod-jenkins:latest").push()
           
                     }
                  }   
               }   
             }
        }
    }
    post { 
        always { 
            echo 'Deleting Project now !! '
            deleteDir()
        }
    }
}

    