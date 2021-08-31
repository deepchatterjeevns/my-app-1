node{
       stage('SCM Checkout'){
     git 'https://github.com/deepchatterjeevns/my-app-1.git'
   }
   stage('Compile-Package'){

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
	    
   stage('Build Docker Imager'){
   sh 'docker build -t deepchatterjee/myweb:0.0.2 .'
   }
   stage('Docker Image Push'){
   withCredentials([string(credentialsId: 'dockerPass', variable: 'dockerPassword')]) {
   sh "docker login -u deepchatterjee -p ${dockerPassword}"
    }
   sh 'docker push deepchatterjee/myweb:0.0.2'
   }
   
   stage('Nexus Image Push'){
   sh "docker login -u admin -p admin123  3.90.212.239:8083"
   sh "docker tag deepchatterjee/myweb:0.0.2  3.90.212.239:8083/deep:1.0.0"
   sh 'docker push  3.90.212.239:8083/deep:1.0.0'
   }
   
   stage('Remove Previous Container'){
	try{
		sh 'docker rm -f tomcattest'
	}catch(error){
		//  do nothing if there is an exception
	}

   stage('Docker deployment'){
   sh 'docker run -d -p 8090:8080 --name tomcattest deepchatterjee/myweb:0.0.2' 
   }
}
}
