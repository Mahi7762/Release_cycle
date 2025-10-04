pipeline {
        agent any
        
		
		stages {
		       stage("Git Checkout") {
			   
			     steps{
			         git branch: '25Q3', url: 'https://github.com/Mahi7762/Release_cycle.git'
			   }
		    } 
		        stage("Copy to HTTPD") {
			   
			     steps{
			        sh '''sudo cp /root/.jenkins/workspace/25Q3/index.html /var/www/html/
			            sudo chmod -R 777 /var/www/html/index.html'''
			   }
		       }
		       
		           stage('Build Docker Image') {
            steps {
                sh '''
                    docker pull ubuntu:latest
                '''
            }
		           }
            stage('Run Docker Container') {
            steps {
                //sh '''docker rm -f  my-httpd3'''

                sh ''' 
                    docker run -d --name my-httpd3 -p 8083:80 httpd:latest
                '''
            }
        }
		        stage("COPY File to HTTPD") {
			   
			     steps{
			        sh '''docker cp /root/.jenkins/workspace/25Q3/index.html my-httpd:/usr/local/apache2/htdocs/'''
			        sh '''docker exec my-httpd chmod 644 /usr/local/apache2/htdocs/index.html'''
			   }
		       }
		}	
}
