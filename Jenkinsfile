pipeline {
    agent {label 'Tools'} 
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

				stage('cleanup Tools'){
					steps{
						dir('/opt/install/'){
							//sh 'ls -l'
							sh 'rm -rf *'
							//sh 'ls -l'
						}
						
					}
					
				}
				stage('Cleanup Product') {
					agent {
						label 'Tools'
					}
					steps {
						script {
							try{

							echo "Start: shutdown CTP"
							sh '/opt/softwareag/profiles/CTP/bin/shutdown.sh'
							echo "Completed: shutdown CTP"

							echo "Start: IS"
							sh '/opt/softwareag/profiles/IS_default/bin/shutdown.sh'
							echo "Completed: shutdown IS"

							echo "Start: SPM"
							sh '/opt/softwareag/profiles/SPM/bin/shutdown.sh'
							echo "Completed: shutdown SPM"
							}
							
						catch(Exception e){

							echo "shutdown failed"
						}

							//disable because IS installation is failing so UM is installed yet
							/*echo "Start: UM"
							sh '/opt/softwareag/UniversalMessaging/server/umserver/bin/nstopserver'
							echo "Completed: shutdown SPM"*/

							dir('/opt/softwareag') {
								//sh 'ls -l'
								sh 'rm -rf *'
								//sh 'ls -l'
							}

							dir('/opt/SAGUpdateManage/'){
								//sh 'ls -l'
								sh 'rm -rf *'
								//sh 'ls -l'
							}
						}
					}
				}
				/*stage('Cleanup MySQL') {
					agent {
						label 'master'
					}
					environment {
						softwareagInstallation="/home/saguser/SoftwareAG103"
					}
					steps {
						script {
							echo "Shutdown CCE"
							sh "${env.softwareagInstallation}/profiles/CCE/bin/shutdown.sh"
							sh "rm -rf ${env.softwareagInstallation}/profiles/CCE/logs/"
						}
					}
				}

				stage('Cleanup OnPremise Designer') {
					agent {
						label 'OnPremDesigner'
					}
					steps {
						script {
							echo "cleanup OnPremDesigner"
							sh 'echo "$pwd"'
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
						//	sh "$pwd"
						}
						
					}
				}
				
				stage ('Cleanup IS + UM on cloud setup'){
					agent{label 'ISUM'}
					steps {
						script {
							echo "Cleanup IS + UM"
							//sh "$pwd"
						}
					}
				}*/
	  		}
		}
		stage('Checkout jobs'){
			parallel{

				stage('Checkout CloudDeployment Automation project') {
					//environment {
					//	workspace="/home/saguser/CloudTransform/"
					//}
					agent {
						label 'Tools'
					}
					steps {
						script {
							dir('/opt/install'){

								echo "Started: checking out the GIT /opt/install/project"
								sh 'git clone --recursive https://github.com/AbhishekGupta1506/CloudTransformCICD.git'
								echo "Done: checking out the GIT project"
								echo "SVN checkout started"
								//svn checkout "http://svndae.hq.sag:1818/svn/sag/integration-live/installation/branches/CloudDeployment/"
								checkout([$class: 'SubversionSCM', locations: [[credentialsId: 'abgWC', local: '.', remote: 'http://svndae.hq.sag:1818/svn/sag/integration-live/installation/trunk/']]])
								echo "SVN checkout done"
								sh 'chmod 777 *'
								sh 'cp /opt/install/CloudTransformCICD/CloudDeploymentAssets/gradle.properties /opt/install'
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
								echo "Started: checking out the GIT project"
								bat 'git clone --recursive https://github.com/AbhishekGupta1506/CloudTransformCICD.git'
								echo "Completed: checking out the GIT project"
							}
						}
					}
				}
			}
		}
		
		stage('Install products') {
			parallel {

				/*stage('Setup MySQL') {
					agent {
						label 'MySQL'
					}
					steps {
						echo "hello MySQL"
					}
				}*/
				/*stage('Install OnPremise Designer') {
					agent {
						label 'OnPremDesigner'
					}
					steps {
						echo "hello OnPremDesigner"
					}
				}*/

				/*stage ('Install CTP on cloud setup'){
					agent {
						label 'CTP'
					}
					steps {
						script {
							dir("${workspace}/Automation"){
								echo "CTP installation started"


							}
						}
					}
				}*/

				/*stage ('Install IS + UM on cloud setup'){
					agent{label 'ISUM'}
					steps {
						echo "hello IS + UM"
					}
				}*/
				
				stage ('Install IS + UM + CTP on cloud setup'){
					agent{label 'Tools'}
					steps {
						dir('/opt/install'){
							echo "Started: CTP installation"
							sh './gradlew installCTP -x validate'
							echo "Completed: CTP installation"

							//removing .svn folder because of IS failure 
							dir('/opt/install/os_independent/packages'){

								sh 'rm -rf *'
							}
							echo "Started: IS installation"
							sh './gradlew installIS -x validate'
							echo "Completed: IS installation"

							echo "Started: UM installation"
							sh './gradlew installUM -x validate'
							echo "Completed: UM installation"
						}
					}
				}
				stage('Installating the Designer') {

					agent {
						label Designer
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