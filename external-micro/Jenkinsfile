pipeline {
  
  agent any
  
  environment {
      port = 8082
  }


  
  stages {
              
    stage('Git') {
      steps {
        git branch: 'main', url: 'https://github.com/kenkool23/capstone.git'
        echo 'cloning git repo'
      }
    }
     
    stage("Setting up npm"){
        steps {
            echo 'Installing npm' 
		     sh 'npm install'
             
            }
    }
    
    stage("Running the tests"){
        steps {
            echo 'Installing npm' 
		    //sh 'npm test'
        }
    }

    

    stage("Build Docker image"){
        steps{
                echo 'Building Docker image' 
                sh 'gcloud builds submit -t gcr.io/capstone-project-308119/external-micro:${BUILD_ID} external-micro'
                echo 'Done' 
            }
          }

    
    stage("Connecting to the cluster"){
        steps {
            echo 'Connecting to the cluster' 
		
		    sh 'gcloud container clusters get-credentials capstone --zone us-central1-c --project capstone-project-308119'
        }
    }
    
    
    stage("Deploying the image"){
        steps {
            echo 'Setting the image' 
		
		    sh 'kubectl set image gcr.io/mar-roidtc307/devoops_internal:latest --record'
		    sh 'kubectl set image gcr.io/capstone-project-308119/external-micro nginx=external-micro:$(BUILD_ID) --record'
        }
    }
    
    
  }

}

------------

pipeline {
  
  agent any
  
  environment {
      port = 8082
  }

  stages {
              
    stage('Git') {
      steps {
        git branch: 'main', url: 'https://github.com/kenkool23/capstone.git'
        echo 'cloning git repo'
      }
    }
     
    stage("Setting up npm"){
        steps {
            echo 'Installing npm' 
             sh 'cd external-micro'
		     sh 'npm install'
             
            }
    }
    
    stage("Running the tests"){
        steps {
            echo 'Installing npm' 
            sh 'cd external-micro'
		    //sh 'npm test'
        }
    }

    

    stage("Build Docker image"){
        steps{
                echo 'Building Docker image' 
                //sh 'docker builds submit -t gcr.io/capstone-project-308119/external-micro:${BUILD_ID} external-micro'
                sh 'cd external-micro'
                sh 'pwd'
                script {
                    myapp = docker.build("kenkool23/external-events:${env.BUILD_ID}")
                }
            }
          }

    stage("Push image") {
        steps {
            script {
                docker.withRegistry('https://registry.hub.docker.com', 'Dockerhub') {
                            myapp.push("latest")
                            myapp.push("${env.BUILD_ID}")
                    }
                }
            }
        }
    
    
    stage("Connecting to the DockerHub"){
        steps {
            echo 'Connecting to the cluster' 
		
		    sh 'gcloud container clusters get-credentials capstone --zone us-central1-c --project capstone-project-308119'
        }
    }
    
    
    stage("Deploying the image"){
        steps {
            echo 'Setting the image' 
				    sh 'kubectl set image gcr.io/capstone-project-308119/external-micro nginx=external-micro:$(BUILD_ID) --record'
				    sh 'kubectl set image deployment/hello-app hello-app=gcr.io/${PROJECT_ID}/hello-app:v2'
        }
    }
    
    
  }

}