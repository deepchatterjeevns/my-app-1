node{
   stage('SCM Checkout'){
     git 'https://github.com/NITHYANANDA18/my-app.git'
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
   sh 'docker build -t nithya18/myweb:0.0.2 .'
   }
   stage('Docker Image Push'){
   withCredentials([string(credentialsId: 'dockerPass', variable: 'dockerPassword')]) {
   sh "docker login -u nithya18 -p ${dockerPassword}"
    }
   sh 'docker push nithya18/myweb:0.0.2'
   }

   stage('Nexus Image Push'){
   sh "docker login -u admin -p admin123  13.126.35.8:8083"
   sh "docker tag nithya18/myweb:0.0.2  13.126.35.8:8083/nit:1.0.0"
   sh 'docker push  13.126.35.8:8083/nit:1.0.0'
   }

   stage('Remove Previous Container'){
	try{
		sh 'docker rm -f tomcattest'
	}catch(error){
		//  do nothing if there is an exception
	}

   stage('Docker deployment'){
   sh 'docker run -d -p 8090:8080 --name tomcattest nithya18/myweb:0.0.2' 
   }
}
}

