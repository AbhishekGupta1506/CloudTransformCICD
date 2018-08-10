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
							sh 'rm -rf *'
						}
						
					}
					
				}

				stage('cleanup Designer Machine'){
					agent {
						label 'Designer'
					}
					steps{
						dir('C:/Cloud/'){
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
							
							

						}
						dir('C:/Cloud') {
									
								if (fileExists('C:\\SoftwareAGCloud')) {
								echo "cleanup Designer"
								bat 'rmdir "C:\\SoftwareAGCloud" /s /q'
								} else {
								echo "Not Found C:\\SoftwareAGCloud dir"
								}						
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
								echo "Start: shutdown CTP"
								//sh '/opt/softwareag/profiles/CTP/bin/shutdown.sh'
								//sh 'PID=$!' //catch the last PID, here from command1
								//sh 'wait $PID' //wait for command1, in background, to end
								echo "Completed: shutdown CTP"
								echo "Start: SPM shutdown"
								//sh '/opt/softwareag/profiles/SPM/bin/shutdown.sh'
								//sh 'PID=$!' //catch the last PID, here from command1
								//sh 'wait $PID' //wait for command1, in background, to end
								echo "Completed: shutdown SPM"
								//remove this once running on WmCI machines
								echo "Start: IS"
								//sh '/opt/softwareag/profiles/IS_default/bin/shutdown.sh'
								//sh 'PID=$!' //catch the last PID, here from command1
								//sh 'wait $PID' //wait for command1, in background, to end
								echo "Completed: shutdown IS"
								echo "Start: UM"
								//sh '/opt/softwareag/UniversalMessaging/server/umserver/bin/nstopserver'
								//sh 'PID=$!' //catch the last PID, here from command1
								//sh 'wait $PID' //wait for command1, in background, to end
								echo "Completed: shutdown UM"
							}
							catch(Exception e){
								echo "CTP cleanup failed"
							}
							
							dir('/opt/softwareag/') {
								//sh 'ls -l'
								echo "cleanup /opt/softwareag dir"
								sh 'rm -rf *'
							}
							dir('/opt/SAGUpdateManage/'){
								//sh 'ls -l'
								echo "cleanup /opt/SAGUpdateManage/ dir"
								sh 'rm -rf *'
							}
							dir('/opt/install/'){
								//sh 'ls -l'
								echo "cleanup checkout /opt/install/ dir"
								sh 'rm -rf *'
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
								echo "Start: IS shutdown"
								//sh '/opt/softwareag/profiles/IS_default/bin/shutdown.sh'
								echo "Completed: shutdown IS"
								echo "Start: UM"
								//sh '/opt/softwareag/UniversalMessaging/server/umserver/bin/nstopserver'
								echo "Completed: shutdown UM"
								echo "Start: CTP"
								//sh '/opt/softwareag/profiles/CTP/bin/shutdown.sh'
								echo "Completed: shutdown CTP"
								echo "Start: SPM"
								//sh '/opt/softwareag/profiles/SPM/bin/shutdown.sh'
								echo "Completed: shutdown SPM"
							}
							catch(Exception e){
								echo "IS or UM cleanup failed"
							}
							
							dir('/opt/softwareag/') {
								//sh 'ls -l'
								echo "cleanup /opt/softwareag dir"
								sh 'rm -rf *'
							}
							dir('/opt/SAGUpdateManage/'){
								//sh 'ls -l'
								echo "cleanup /opt/SAGUpdateManage/ dir"
								sh 'rm -rf *'
							}
							dir('/opt/install/'){
								//sh 'ls -l'
								echo "cleanup checkout /opt/install/ dir"
								sh 'rm -rf *'
							}
						}						
					}
				}
	  		}
		}
		stage('Checkout jobs'){
			parallel{

				stage('Checkout CloudDeployment Automation project') {
					//environment {
					//	workspace="/home/saguser/CloudTransform/"
					//}
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
								sh 'cp /opt/install/CloudTransformCICD/CloudDeploymentAssets/gradle.properties /opt/install'
								sh 'cp /opt/install/CloudTransformCICD/CloudDeploymentAssets/default_gradle.properties /opt/install'
								sh 'cp /opt/install/CloudTransformCICD/CloudDeploymentAssets/um.txt /opt/install/templates/um.txt'
								echo "Run gradlew -b download.gradle download"
								sh './gradlew -b download.gradle download'
								echo "Completed gradlew -b download.gradle download"
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
						dir('/opt/install'){
							echo "Started: CTP installation"
							sh './gradlew installCTP -x validate'
							echo "Completed: CTP installation"

							echo "Started: UM installation"
							sh './gradlew installUM -x validate'
							echo "Completed: UM installation"
							//removing .svn folder because of IS failure 
							dir('/opt/install/os_independent/packages/'){

								sh 'rm -rf .svn'
								sh 'ls -al'
							}
							echo "Started: IS installation"
							sh './gradlew installIS -x validate'
							echo "Completed: IS installation"

							
						}
					}
				}
				stage ('Install IS+UM on cloud setup'){
					agent{label 'ISUM'}
					steps {
						dir('/opt/install'){
							//removing .svn folder because of IS failure 
							/*dir('/opt/install/os_independent/packages'){

								sh 'rm -rf *'
							}
							echo "Started: IS installation"
							sh './gradlew installIS -x validate'
							echo "Completed: IS installation"

							echo "Started: UM installation"
							sh './gradlew installUM -x validate'
							echo "Completed: UM installation"*/
							echo "Started: Install IS+UM on cloud setup"
							echo "Completed: Install IS+UM on cloud setup"
						}
					}
				}
				stage ('Run MySql script on cloud setup'){
					agent{label 'MySql'}
					steps {
						dir('/opt/install'){
							echo "Started: MySql script"
							echo "Completed: MySql script"
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