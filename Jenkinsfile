pipeline 
{
    agent any 
    tools
	  {
      maven "Maven_var"
	  }
    
    stages {

        stage('Checkout GIT') 
		{ 
            steps 
			{
				  echo 'Pulling From Git'
          git credentialsId: 'yourCredentialId', url: 'GithubRepoUrl'
      }

		stage("Maven Build") 
		{
            steps
			 {
				  sh "mvn clean install -DskipTests -P yourMavenRepo"
				  echo'test'
        }
    }
		

	  stage('SonarQube analysis') 
	  {
           steps 
			{
          withSonarQubeEnv('Sonar Server') 
				  {
              sh """mvn sonar:sonar"""
          }
      }
    }
	    
	  stage('Docker Image Build') 
	  {
    
      	  steps
		      {
        			sh ' cp yourJarPath yourWorkspacePath '
              echo 'Create a Docker Image '
              sh ' sudo docker build -t yourImageName .'
              echo 'Create tag to push to Dockerhub'
              sh ' docker tag yourImageName dockerhubRepo:tag'
              echo ' Push docker image '
              withCredentials([usernamePassword(credentialsId: 'yourCredentialId', usernameVariable: 'DOCKER_USERNAME', passwordVariable: 'DOCKER_PASSWORD')])
              {
              sh "docker login -u ${DOCKER_USERNAME} -p ${DOCKER_PASSWORD}"
              sh "docker push dockerhubRepo:tag"
              }
      	  }
    }

    stage('deploy')
    {
      steps
      {
        sshPublisher(publishers: [sshPublisherDesc(configName: 'ansible_server', transfers: [sshTransfer(cleanRemote: false, excludes: '', execCommand: '''ansible-playbook pathToAnsiblePlaybook ''', execTimeout: 120000, flatten: false, makeEmptyDirs: false, noDefaultExcludes: false, patternSeparator: '[, ]+', remoteDirectory: '', remoteDirectorySDF: false, removePrefix: '', sourceFiles: '')], usePromotionTimestamp: false, useWorkspaceInPromotion: false, verbose: false)])
      }
    }
	    
	    
    }
	
	post 
	{
    		always {
       			mail to: 'Your Mail',
          		subject: "Status of pipeline: ${currentBuild.fullDisplayName}",
         		 body: "${env.BUILD_URL} has result ${currentBuild.result}"
    			}
  	}
}
