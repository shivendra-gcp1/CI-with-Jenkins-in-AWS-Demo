pipeline {
    agent any 	
	environment {
		
		PROJECT_ID = 'git-261005'
                CLUSTER_NAME = 'kubernetes-cluster'
                LOCATION = 'australia-southeast1-b'
                CREDENTIALS_ID = 'kubernetes'		
	}
	
    stages {	
	   stage('Scm Checkout') {            
		steps {
                  checkout scm
		}	
           }
           
	   stage('Build') { 
                steps {
                  echo "Cleaning and packaging stage"
                  sh 'mvn clean package'		
                }
           }
	   stage('Test') { 
		steps {
	          echo "Testing stage"
		  sh 'mvn test'
		}
	   }
	   stage('Build Docker Image') { 
		steps {
                   script {
		      //myimage = docker.build("tejasshahdevops/devops:${env.BUILD_ID}")
			myimage = docker.build("gcr.io/git-261005/tejasshahdevops/devops:${env.BUILD_ID}")
                   }
                }
	   }
	   stage("Push Docker Image") {
                steps {
                   script {
                      /*docker.withRegistry('https://registry.hub.docker.com', 'Docker') {
			   myimage.push("${env.BUILD_ID}")		
                     }*/
		     docker.withRegistry('https://gcr.io', 'gcr:gcrcredential') {   
                            myimage.push("${env.BUILD_ID}")		
                     }
			   
                   }
                }
            }
	   
           stage('Deploy to K8s') { 
                steps{
                   echo "Deployment started ..."
		   sh 'ls -ltr'
		   sh 'pwd'
		   sh "sed -i 's/tagversion/${env.BUILD_ID}/g' deployment.yaml"
                   step([$class: 'KubernetesEngineBuilder', projectId: env.PROJECT_ID, clusterName: env.CLUSTER_NAME, location: env.LOCATION, manifestPattern: 'deployment.yaml', credentialsId: env.CREDENTIALS_ID, verifyDeployments: true])
		   echo "Deployment Finished ..."
            }
          }
    }
}
