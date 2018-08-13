pipeline {
    agent any
    //triggers { pollSCM('H */60 * * 1-5') }
    options {
        buildDiscarder(logRotator(numToKeepStr:'10'))
        disableConcurrentBuilds()
        skipDefaultCheckout() // FIXME: resolve default git checkout problem!
    }

	//tools is ignore if agent is none
	tools {
        jdk 'jdk-1.8'
		gradle 'gradle-3.4.1'

    }
	environment {
						workspace="/home/saguser/CloudTransform/"
				}
    stages {


		stage('Cleanup products') {
			parallel {

				stage('cleanup MySql'){
					agent {
						label 'MySql'
					}
					steps{
						
					
						dir('/opt/install/'){
							//sh 'ls -l'
							echo "cleanup checkout /opt/install/ dir"
							
							//sh './gradlew -b download.gradle download'
							//sh './gradlew dropDatabases -x validate'
							//sh 'rm -rf *'

							// code for rever VMs 
						}
						if (fileExists('/root/.gradle')) {
								sh 'rm -rf /root/.gradle' //cleanup /root/.gradle
						}
					}
					
				}

				stage('cleanup Designer Machine'){
					agent {
						label 'Designer'
					}
					steps{
						script{
						//dir('C:/Cloud/'){
							//sh 'ls -l'

							if (fileExists('C:\\CloudCheckOut')) {
							echo "cleanup C:\\CloudCheckOut dir"
							bat 'rmdir "C:\\CloudCheckOut" /s /q'
							} else {
							echo "Not Found C:\\CloudCheckOut dir"
							}

							if (fileExists('C:\\CloudCheckOut@tmp')) {
							echo "cleanup C:\\CloudCheckOut@tmp dir"
							bat 'rmdir "C:\\CloudCheckOut@tmp" /s /q'
							} else {
							echo "Not Found C:\\CloudCheckOut@tmp dir"
							}
							
							

						//}
						//dir('C:/Cloud') {
									
								if (fileExists('C:\\SoftwareAGCloud')) {
								echo "cleanup Designer"
								bat 'rmdir "C:\\SoftwareAGCloud" /s /q'
								} else {
								echo "Designer is not installed under dir C:\\SoftwareAGCloud"
								}						
						//}
						}
						
					}
					
				}
				stage ('Cleanup CTP on cloud setup'){
					agent {
						label 'CTP'
					}
					steps {
						script {
							echo "Cleanup CTP"
							try{

								// code for rever VMs 

								if (fileExists('/root/.gradle')) {
									sh 'rm -rf /root/.gradle' //cleanup /root/.gradle
								}
								
							}
							catch(Exception e){
								echo "CTP cleanup failed"
							}
						}
						
					}
				}
				stage ('Cleanup IS+UM on cloud setup'){
					agent {
						label 'ISUM'
					}
					steps {
						script {
							echo "Cleanup IS+UM"

							
							try{

								// code for rever VMs 

								if (fileExists('/root/.gradle')) {
									sh 'rm -rf /root/.gradle' //cleanup /root/.gradle
								}
							}
							catch(Exception e){
								echo "IS or UM cleanup failed"
							}
						}						
					}
				}
	  		}
		}
		stage('Checkout jobs'){
			parallel{

				stage('Checkout CloudDeployment Automation project') {
					agent {
						//label 'MySql && CTP && ISUM'
						label 'CTP'
					}
					steps {
						script {
							dir('/opt/install'){

								echo "Started: checking out the GIT /opt/install/project"
								sh 'git clone -b dev --recursive https://github.com/AbhishekGupta1506/CloudTransformCICD.git'
								echo "Done: checking out the GIT project"
								echo "SVN checkout started"
								//svn checkout "http://svndae.hq.sag:1818/svn/sag/integration-live/installation/branches/CloudDeployment/"
								checkout([$class: 'SubversionSCM', locations: [[credentialsId: 'abgWC', local: '.', remote: 'http://svndae.hq.sag:1818/svn/sag/integration-live/installation/trunk/']]])
								echo "SVN checkout done"
								sh 'chmod 777 *'
								//remove once automation is fixed from WCIC team
								sh 'cp /opt/install/CloudTransformCICD/CloudDeploymentAssets/CTP/gradle.properties /opt/install'
							}
						}
					}
				}

				stage('Checkout CloudDeployment Automation project') {
					agent {
						//label 'MySql && CTP && ISUM'
						label 'MySQL'
					}
					steps {
						script {
							dir('/opt/install'){

								echo "Started: checking out the GIT /opt/install/project"
								sh 'git clone -b dev --recursive https://github.com/AbhishekGupta1506/CloudTransformCICD.git'
								echo "Done: checking out the GIT project"
								echo "SVN checkout started"
								//svn checkout "http://svndae.hq.sag:1818/svn/sag/integration-live/installation/branches/CloudDeployment/"
								checkout([$class: 'SubversionSCM', locations: [[credentialsId: 'abgWC', local: '.', remote: 'http://svndae.hq.sag:1818/svn/sag/integration-live/installation/trunk/']]])
								echo "SVN checkout done"
								sh 'chmod 777 *'
								//remove once automation is fixed from WCIC team
								sh 'cp /opt/install/CloudTransformCICD/CloudDeploymentAssets/SQL/gradle.properties /opt/install'
							}
						}
					}
				}

				tage('Checkout CloudDeployment Automation project') {
					agent {
						//label 'MySql && CTP && ISUM'
						label 'ISUM'
					}
					steps {
						script {
							dir('/opt/install'){

								echo "Started: checking out the GIT /opt/install/project"
								sh 'git clone -b dev --recursive https://github.com/AbhishekGupta1506/CloudTransformCICD.git'
								echo "Done: checking out the GIT project"
								echo "SVN checkout started"
								//svn checkout "http://svndae.hq.sag:1818/svn/sag/integration-live/installation/branches/CloudDeployment/"
								checkout([$class: 'SubversionSCM', locations: [[credentialsId: 'abgWC', local: '.', remote: 'http://svndae.hq.sag:1818/svn/sag/integration-live/installation/trunk/']]])
								echo "SVN checkout done"
								sh 'chmod 777 *'
								//remove once automation is fixed from WCIC team
								sh 'cp /opt/install/CloudTransformCICD/CloudDeploymentAssets/ISUM/gradle.properties /opt/install'
								sh 'cp /opt/install/CloudTransformCICD/CloudDeploymentAssets/ISUM/um.txt /opt/install/templates/um.txt'
							}
						}
					}
				}

				stage('checkout designer'){
					agent{
						label 'Designer'
					}
					steps{
						script{
							dir('C:/CloudCheckOut'){
								echo "Started: checking out the GIT project in designer setup"
								bat 'git clone -b dev --recursive https://github.com/AbhishekGupta1506/CloudTransformCICD.git'
								echo "Completed: checking out the GIT project in designer setup"
							}
						}
					}
				}
			}
		}
		
		stage('Install products') {
			parallel {
				
				stage ('Install CTP on cloud setup'){
					agent{label 'CTP'}
					steps {

						sh 'postfix start'
						sh 'memcached -d -u root -m 256'

						dir('/opt/install'){
							
							echo "download assets job start"
							sh './gradlew -b download.gradle download'
							echo "download job done"

							echo "Started: CTP installation"
							sh './gradlew installCTP -x validate'
							echo "Completed: CTP installation"

							echo "Started: IS war installation"
							sh './gradlew installIntegrationWar -x validate'
							echo "Completed: IS war installation"

							echo "Started: customizeCTP job started"
							sh './gradlew customizeCTP -x validate'
							echo "Completed: customizeCTP job"

							
						}
					}
				}

				stage ('Run MySql script on cloud setup'){
					agent{label 'MySql'}
					steps {
						sh '/etc/init.d/mysql stop'
						sh 'mysqld --defaults-file=/usr/my-ipaas.ini -u root 2>1 &'

						dir('/opt/install'){
							echo "Started: MySql script"
							//sh './gradlew -b download.gradle download' 
							sh './gradlew executeDatabaseScripts -x validate'
							sh './gradlew executeCustomSQLScripts -x validate'
							sh './gradlew apply_10_7_patch -x validate'
							sh './gradlew apply_10_8_patch -x validate'
							sh './gradlew executeDatabasePatchScripts -x validate'
							sh './gradlew customizeDB -x validate'
							echo "Completed: MySql script"



						}

							echo "Stop Nginx started"
							sh 'service nginx stop'
							echo "Stop Nginx completed"

							echo "Start Nginx started"
							sh 'service nginx start'
							echo "Completed Nginx start"
					}
				}

				stage ('Install IS+UM on cloud setup'){
					agent{label 'ISUM'}
					steps {

						echo "Stop Nginx started"
						sh 'service nginx stop'
						echo "Stop Nginx completed"

						echo "Start Nginx started"
						sh 'service nginx start'
						echo "Completed Nginx start"

						dir('/opt/install'){
							echo "Started: Install IS+UM on cloud setup"
							sh './gradlew installIS -x validate'
							sh './gradlew installUM -x validate'
							echo "Completed: Install IS+UM on cloud setup"
						}
					}
				}
				stage('Installing the Designer') {

					agent {
						label 'Designer'
					}
					steps{
						script{
							dir('C:/CloudCheckOut/CloudTransformCICD/installer'){
								echo "Start: Designer installation"
								bat 'ant install.designer'
								echo "Completed: Designer installation"
							}
						}
					}
				}

			}
	  }
		/*stage('Run tests') {
			agent{
				label 'OnPremDesigner'
			}
			steps {
				echo "Run the the test cases here"
			}
		}*/
	}
	post {	
				always {
						//mail to: 'abg@softwareag.com, mjan@softwareag.com, mosy@softwareag.com, meea@softwareag.com, inra@softwareag.com',
						mail to: 'abg@softwareag.com',
						subject: "CloudTransformation Pipeline completed -- JobName: ${env.JOB_NAME} BuildNumber: ${env.BUILD_NUMBER}",
						body: "checkout the report: ${env.BUILD_URL}"
			}
	}
}