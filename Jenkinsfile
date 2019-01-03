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
                    sh 'mvn -DskipTests cobertura:cobertura -Dcobertura.report.format=xml'
                    cobertura coberturaReportFile: 'target/site/cobertura/coverage.xml'
                }
            }    	

	stage('Sonar Analysis') {
    	    agent any
    	        steps {
                    withSonarQubeEnv('sonar') {
                    sh 'mvn -DskipTests sonar:sonar'
                    }
                }
            } 
	    
	stage('Generate Documentation') {
    	    agent any
                steps {
                    sh 'mvn javadoc:javadoc'
                    step([$class: 'JavadocArchiver', javadocDir: 'target/site/apidocs', keepAll: false])
                }
            }
        stage('Build & Publish ') {
    	    agent any
                steps {
                    script {
                    env.VERSION = readMavenPom().getVersion()
                }
                    echo 'Publishing the Artficat: ' + VERSION
                    sh 'mvn -DskipTests package'
                    nexusPublisher nexusInstanceId: 'nexus', nexusRepositoryId: 'releases', packages: [
     [$class: 'MavenPackage', mavenAssetList: [
      [classifier: '', extension: '', filePath: '/var/lib/jenkins/workspace/SpringAppGit/target/SpringHibernateExample-' + VERSION + '.war']
     ], mavenCoordinate: [artifactId: 'SpringHibernateExample', groupId: 'com.websystique.springmvc', packaging: 'war', version: VERSION]]
    ]
                }
            }

	stage('Deploy To App Server') {
    	    agent any
                steps {
                    sh 'sudo cp /var/lib/jenkins/workspace/SpringAppGit/target/*.war /home/ninja/apache-tomcat-7.0.92/webapps'
                }
            }

    }
}
