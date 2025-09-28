pipeline {

    agent any
	tools {
		maven 'maven_3.9.11'
	}
    stages {
	
        stage('CLONE SCM') {
            steps {
                echo 'This stage clones SC from GIT repo'				
				git branch: 'main', url: 'https://github.com/supriyan3/mindcircuit16d.git'
            }
        }
		
        stage('Build Artifact') {
            steps {
                echo 'This stage builds the code using maven'
				sh 'mvn clean install'			
				
            }
        }
		
        stage('Deploy to Tomcat') {
            steps {
                echo 'This stage deploys .war to tomcat webserver'
                deploy adapters: [tomcat9(alternativeDeploymentContext: '', credentialsId: 'tomcat', path: '', url: 'http://52.91.3.127:8082/')], contextPath: 'MC-APP', war: '**/*.war'
            }
        }		
		
    }
}
