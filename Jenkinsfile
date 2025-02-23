pipeline {
  agent any
  tools { 
        maven 'Maven_3_2_5'  
    }
   stages{
  stage('CompileandRunSonarAnalysis') {
             steps {	
		sh 'mvn clean verify sonar:sonar -Dsonar.projectKey=secchampquantum -Dsonar.organization=secchampquantum -Dsonar.host.url=https://sonarcloud.io -Dsonar.token=93650f4e4920a3da1eeb0ccaa5a303906088686c'
			}
        } 

	stage('RunSCAAnalysisUsingSnyk') {
          steps {		
				withCredentials([string(credentialsId: 'SNYK_TOKEN', variable: 'SNYK_TOKEN')]) {
					sh 'mvn snyk:test -fn'
				}
			}

    }	

	stage('Build') { 
            steps { 
               withDockerRegistry([credentialsId: "dockerlogin", url: ""]) {
                 script{
                 app =  docker.build("secchampquantum")
                 }
               }
            }
    }

	stage('Push') {
    steps {
        script {
            // Get access_key_id/secret from environment varialbe or set it up as AWS credentials in Jenkins
            sh '''
                AWS_ACCESS_KEY_ID={AWS_ACCESS_KEY_ID} \
                AWS_SECRET_ACCESS_KEY={AWS_SECRET} \
                AWS_DEFAULT_REGION=us-west-2 \
                aws ecr get-login-password --region us-west-2 | docker login --username AWS --password-stdin 022499043590.dkr.ecr.us-west-2.amazonaws.com
            '''
            
            // Pushing the image
            sh 'docker tag secchampquantum:latest 022499043590.dkr.ecr.us-west-2.amazonaws.com/secchampquantum:latest'
            sh 'docker push 022499043590.dkr.ecr.us-west-2.amazonaws.com/secchampquantum:latest'
        }
    }
}

	    
  }
}
