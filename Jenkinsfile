pipeline {
    agent none    
    
    stages {
        
    	stage('Get Code') {
    	    agent any
                steps {
                    git branch: 'master', url: 'https://github.com/devopsguy9/Spring-Hibernate-App.git'
                }
            }
            
        stage('Maven Compile') {
    	    agent any
                steps {
                    sh 'mvn compile'
                }
            }	

        stage('Maven Test') {
    	    agent any
                steps {
                    sh 'mvn test'
                }
                
            post {
                always {
                    junit 'target/surefire-reports/*.xml'
                }
            }

            }

	stage('Code Coverage') {
    	    agent any
                steps {
                    sh 'mvn cobertura:cobertura -Dcobertura.report.format=xml'
                    cobertura coberturaReportFile: 'target/site/cobertura/coverage.xml'
                }
            }    	
    }
}
