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
								echo "SVN checkout started"
								//svn checkout "http://svndae.hq.sag:1818/svn/sag/integration-live/installation/branches/CloudDeployment/"
								checkout([$class: 'SubversionSCM', locations: [[credentialsId: 'abgWC', local: '.', remote: 'http://svndae.hq.sag:1818/svn/sag/integration-live/installation/trunk/']]])
								echo "SVN checkout done"
								sh 'chmod 777 *'
								echo "Run gradlew -b download.gradle download"
								sh './gradlew -b download.gradle download'
								echo "Completed gradlew -b download.gradle download"
							}
						}
					}
			}
	/*	stage('Cleanup products') {
			parallel {

      	stage('Cleanup MySQL') {
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
				}
			}
	  }*/

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
							//sh './gradlew installCTP -x validate'
							echo "Completed: CTP installation"

							echo "Started: IS installation"
						//	sh './gradlew installIS -x validate'
							echo "Completed: IS installation"

							echo "Started: UM installation"
							//sh './gradlew installUM -x validate'
							echo "Completed: UM installation"
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