node{
     stage('SCM Checkout'){
     git 'https://github.com/naveenraj80/my-app.git'
    }
     stage('maven-buildstage'){
      def mvnHome =  tool name: 'maven3', type: 'maven'   
      sh "${mvnHome}/bin/mvn clean package"
	  sh 'mv target/myweb*.war target/newapp.war'
    }
     stage('SonarQube Analysis') {
	  def mvnHome =  tool name: 'maven3', type: 'maven'
	  withSonarQubeEnv('sonar') { 
	  sh "${mvnHome}/bin/mvn sonar:sonar"
	        }
    }
      stage('Build Docker Image'){
      sh 'docker build -t naveen/newapp .'
    }
      stage('Docker Image Push'){
      withCredentials([string(credentialsId: 'dockerPass', variable: 'dockerPassword')]) {
      sh "docker login -u naveenraj80 -p ${dockerPassword}"
      sh "docker tag naveen/newapp naveenraj80/devops:my-img2"
      sh 'docker push naveenraj80/devops:my-img2'
      }
    }
      stage('Nexus Image Push'){
      sh "docker login -u admin -p 12345 52.65.176.221:1896"
      sh "docker tag naveenraj80/devops:my-img2 52.65.176.221:1896/naveen:1.0.0"
      sh 'docker push 52.65.176.221:1896/naveen:1.0.0'
   }
      stage('Remove Previous Container'){
	try{
		sh 'docker rm -f tomcatlatest18'
	}catch(error){
		//  do nothing if there is an exception
	 }
    }
      stage('Docker deployment'){
      sh 'docker run -d -p 4559:8080 --name tomcatlatest19 naveen/newapp' 
      }
    }
