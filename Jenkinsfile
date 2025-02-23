pipeline {
  agent any
  tools { 
        maven 'Maven_3_2_5'  
    }
   stages{
    stage('CompileandRunSonarAnalysis') {
            steps {	
		sh 'mvn clean verify sonar:sonar -Dsonar.projectKey=asgbuggywebapp -Dsonar.organization=asgbuggywebapp -Dsonar.host.url=https://sonarcloud.io -Dsonar.token=932558e169d66a8f1d1adf470b908a46156f5844'
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
            // Hardcoding AWS credentials (temporary solution)
            sh '''
                AWS_ACCESS_KEY_ID=AKIAQKPIMFEDDKMFJQ2N \
                AWS_SECRET_ACCESS_KEY=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 \
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
