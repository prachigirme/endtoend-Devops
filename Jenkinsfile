currentBuild.displayName = "Final_Demo # "+currentBuild.number

   def getDockerTag(){
        def tag = sh script: 'git rev-parse HEAD', returnStdout: true
        return tag
        }
        

pipeline{
        agent any  
        environment{
	    Docker_tag = getDockerTag()
        }
        
        stages{


              stage('Maven'){

               agent {
                docker {
                image 'maven:3-openjdk-11'
                args '-v $HOME/.m2:/root/.m2'
                }
            }
                  steps{
                      
		    sh "mvn clean install"
                  }
                }  
              



              stage('Docker build')
                {
              steps{
                  script{
		 sh 'cp -r ../endtoend@2/target .'
                   sh 'docker build . -t prachi11/devopsproject:$Docker_tag'
		   withCredentials([string(credentialsId: 'dockerhub', variable: 'dockerhub')]) {
				    
				  sh 'docker login -u prachi11 -p $dockerhub'
				  sh 'docker push prachi11/devopsproject:$Docker_tag'
			}
                       }
                    }
                 }
	
		 
		stage('ansible playbook'){
			steps{
			 	script{
				    sh '''final_tag=$(echo $Docker_tag | tr -d ' ')
				     echo ${final_tag}test
				     sed -i "s/docker_tag/$final_tag/g"  deployment.yaml
				     '''
                                    ansiblePlaybook become: true, credentialsId: '24c71f8b-1124-4160-bc1a-ac602742fa99', installation: 'ansible', inventory: 'hosts', playbook: 'ansible.yaml'				}
			}
		}
		
	}
		
}
	       
	       
	       
	      
    

