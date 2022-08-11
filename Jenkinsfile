pipeline { 

    environment { 

     
		   AWS_ACCOUNT_ID="437311381503"
        AWS_DEFAULT_REGION="ap-south-1" 
	CLUSTER_NAME="demo"
	SERVICE_NAME="demoapp"
	TASK_DEFINITION_NAME="demoapp"
	DESIRED_COUNT="1"
        IMAGE_REPO_NAME="demoapp"
        IMAGE_TAG="${env.BUILD_ID}"
        REPOSITORY_URI = "${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_DEFAULT_REGION}.amazonaws.com/${IMAGE_REPO_NAME}"
	registryCredential = "aws"

    }

    agent any 
	 tools
    {
       maven "Maven"
    }

    stages { 

        stage('Cloning our Git') { 

            steps { 

                git 'https://github.com/sandeepelemaneaws4/sampleapp.git' 

            }

        } 
		
		stage('Tools Init') {
            steps {
                script {
                    echo "PATH = ${PATH}"
                    echo "M2_HOME = ${M2_HOME}"
               
                    
            }
            }
        }
     
        
         stage('Execute Maven') {
           steps {
             
                sh 'mvn package'             
          }
        }
        

        stage('Building our image') { 

            steps { 

                script { 

                    dockerImage = docker.build "${IMAGE_REPO_NAME}:${IMAGE_TAG}"

                }

            } 

        }

        stage('Deploy our image') { 

            steps { 

                script { 

                    docker.withRegistry("https://" + REPOSITORY_URI, "ecr:${AWS_DEFAULT_REGION}:" + registryCredential) { 

                        dockerImage.push() 

                    }

                } 

            }
        }
        
       stage('Deploy') {
          steps {
               withAWS(credentials: registryCredential, region: "${AWS_DEFAULT_REGION}") {
                script {
                    sh 'chmod 777 script.sh'
			sh './script.sh'
                }
            } 
        }
      }  
        } 

    

    }

