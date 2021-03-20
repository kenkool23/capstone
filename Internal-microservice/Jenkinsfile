pipeline {
  
  agent any
  
  environment {
      port = 8082
  }

  
 stages {
     
    stage("Setting up npm"){
        steps {
            echo 'Installing npm' 
		
		    sh 'npm install'
             
            }
    }
    
    stage("Running the tests"){
        steps {
            echo 'Installing npm' 
		
		    sh 'npm test'
        }
    }

    stage("Setting env variables"){
        steps{
                echo 'Retrieving source from github' 
                git branch: 'master',
                url: 'https://github.com/HUSunil/Team_Dev_Oops_Internal.git/'
                echo 'Did we get the source?' 
                sh 'ls -a'
            }
          }
    

    stage("Build Docker image"){
        steps{
                echo 'Building Docker image' 
                sh 'gcloud builds submit -t gcr.io/mar-roidtc307/devoops_internal:latest .'
                echo 'Done' 
            }
          }

    
    stage("Connecting to the cluster"){
        steps {
            echo 'Connecting to the cluster' 
		
		    sh 'gcloud container clusters get-credentials devsoops --zone us-central1-a --project mar-roidtc307'
        }
    }
    
    
    stage("Deploying the image"){
        steps {
            echo 'Setting the image' 
		
		    //sh 'kubectl set image gcr.io/mar-roidtc307/devoops_internal:latest --record'
		    sh 'kubectl apply -f internal-deployment.yaml'
        }
    }
    
    

  }

}