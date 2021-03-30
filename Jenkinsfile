pipeline {
  
  agent any
  
  environment {
    PROJECT_ID = 'capstone-project-308119'
    CLUSTER_NAME = 'capstone'
    LOCATION = 'us-central1-c'
    CREDENTIALS_ID = 'capstone-project-308119'
     port = 8082
  }

  stages {
              
    stage('Git') {
      steps {
        git branch: 'main', url: 'https://github.com/kenkool23/front-end.git'
        echo 'cloning git repo'
      }
    }
     
    stage("Setting up npm"){
        steps {
            echo 'Installing npm' 
		     //sh 'npm install'
             
            }
    }
    stage("Running the tests"){
        steps {
            echo 'Installing npm' 
            sh 'npm install'
		    sh 'npm test'
        }
    }
    stage ('SonarQube Analysis') {
    
        steps {
    
              withSonarQubeEnv('new-token') {
    
                //sh "cd test"
                sh 'npm install sonarqube-scanner --save-dev'
                sh 'npm i sonar-project-properties'
                sh 'npm run coverage'
                //sh 'npm install -D sonarqube-scanner'
                //sh 'npm install -g sonarqube-scanner'
    
              }
    
            }
    
      }
    stage("Build Docker image"){
        steps{
             
             script {
                    myapp=docker.build("kenkool23/external-events:${env.BUILD_ID}")
                }
            }
          }

    stage("Push image") {
        steps {
            script {
                docker.withRegistry('https://registry.hub.docker.com', 'Dockehub') {
                            myapp.push("latest")
                            myapp.push("${env.BUILD_ID}")
                    }
                }
            }
        }
    
    stage('Manual Approval') {
        steps {
            script {
              timeout(time: 10, unit: 'MINUTES') {
                input(id: "Deploy Gate", message: "Deploy ${env.JOB_NAME}?", ok: 'Deploy')
              }
            }
        }
    }
    
    stage('Deploy to GKE') {
        steps{
            sh "sed -i 's/external-events:latest/external-events:${env.BUILD_ID}/g' external-deployment.yaml"
            step([$class: 'KubernetesEngineBuilder', projectId: env.PROJECT_ID, clusterName: env.CLUSTER_NAME, location: env.LOCATION, manifestPattern: 'external-deployment.yaml', credentialsId: env.CREDENTIALS_ID, verifyDeployments: true])
        }
    }
    
  }

}