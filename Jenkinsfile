pipeline {
  agent none 
  stages {

		stage('Checkout CloudDeployment Automation project') {
					agent {
						label 'master'
					}
					environment {
						workspace="/home/saguser/CloudTransform/"
					}
					steps {
						script {
							echo "SVN checkout"
							//svn checkout "http://svndae.hq.sag:1818/svn/sag/integration-live/installation/branches/CloudDeployment/"
							//checkout([$class: 'SubversionSCM', locations: [[credentialsId: 'svn-server', local: '.', remote: 'http://svndae.hq.sag:1818/svn/sag/integration-live/installation/branches/CloudDeployment']]])
						}
					}
				}
		stage('Cleanup products') {
			parallel {

      	stage('Cleanup Test') {
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
	  }

		stage('Install products') {
			parallel {

				stage('Install Test') {
					agent {
						label 'master'
					}
					environment{
						softwareagInstallation="/home/saguser/SoftwareAG103"
					}
					steps {
						sh "${env.softwareagInstallation}/profiles/CCE/bin/startup.sh"
					}
				}

				stage('Install OnPremise Designer') {
					agent {
						label 'OnPremDesigner'
					}
					steps {
						echo "hello OnPremDesigner"
					}
				}

				stage ('Install CTP on cloud setup'){
					agent {
						label 'CTP'
					}
					steps {
						echo "hello CTP"
					}
				}
				
				stage ('Install IS + UM on cloud setup'){
					agent{label 'ISUM'}
					steps {
						echo "hello IS + UM"
					}
				}

			}
	  }

		stage('Run tests') {
			agent{
				label 'OnPremDesigner'
			}
			steps {
				echo "Run the the test cases here"
			}
		}
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