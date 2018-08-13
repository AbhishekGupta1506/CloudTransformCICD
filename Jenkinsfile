pipeline {
    agent none
    options {
        buildDiscarder(logRotator(numToKeepStr:'10'))
        disableConcurrentBuilds()
        skipDefaultCheckout()
    }

    stages {

		stage('Restart VMs') {
					agent {
						label 'master'
					}
					steps{
							vSphere buildStep: [$class: 'PowerOff', evenIfSuspended: false, shutdownGracefully: false, vm: 'VMCTP'], serverName: 'daevcsa01.eur.ad.sag'
     						vSphere buildStep: [$class: 'PowerOff', evenIfSuspended: false, shutdownGracefully: false, vm: 'VMSQL'], serverName: 'daevcsa01.eur.ad.sag'
							vSphere buildStep: [$class: 'PowerOff', evenIfSuspended: false, shutdownGracefully: false, vm: 'VMISUM'], serverName: 'daevcsa01.eur.ad.sag'
							//vSphere buildStep: [$class: 'PowerOff', evenIfSuspended: false, shutdownGracefully: false, vm: 'VMDESIGNER'], serverName: 'daevcsa01.eur.ad.sag'
							sleep 10
							vSphere buildStep: [$class: 'PowerOn', timeoutInSeconds: 180, vm: 'VMCTP'], serverName: 'daevcsa01.eur.ad.sag'
							vSphere buildStep: [$class: 'PowerOn', timeoutInSeconds: 180, vm: 'VMSQL'], serverName: 'daevcsa01.eur.ad.sag'
							vSphere buildStep: [$class: 'PowerOn', timeoutInSeconds: 180, vm: 'VMISUM'], serverName: 'daevcsa01.eur.ad.sag'
							//vSphere buildStep: [$class: 'PowerOn', timeoutInSeconds: 180, vm: 'VMDESIGNER'], serverName: 'daevcsa01.eur.ad.sag'
							sleep 80
					}
		}
		stage('Checkout jobs'){
			parallel{

				stage('Checkout CloudDeployment Automation project') {
					agent {
						label 'CTP'
					}
					steps {
						script {
							dir('/opt/install'){
								sh 'git clone -b dev --recursive https://github.com/AbhishekGupta1506/CloudTransformCICD.git'
								checkout([$class: 'SubversionSCM', locations: [[credentialsId: 'abgWC', local: '.', remote: 'http://svndae.hq.sag:1818/svn/sag/integration-live/installation/trunk/']]])
								sh 'chmod 777 *'
								//remove once automation is fixed from WCIC team
								sh 'cp /opt/install/CloudTransformCICD/CloudDeploymentAssets/CTP/gradle.properties /opt/install'
							}
						}
					}
				}

				stage('Checkout CloudDeployment Automation project') {
					agent {
						label 'MySQL'
					}
					steps {
						script {
							dir('/opt/install'){
								sh 'git clone -b dev --recursive https://github.com/AbhishekGupta1506/CloudTransformCICD.git'
								checkout([$class: 'SubversionSCM', locations: [[credentialsId: 'abgWC', local: '.', remote: 'http://svndae.hq.sag:1818/svn/sag/integration-live/installation/trunk/']]])
								sh 'chmod 777 *'
								//remove once automation is fixed from WCIC team
								sh 'cp /opt/install/CloudTransformCICD/CloudDeploymentAssets/SQL/gradle.properties /opt/install'
							}
						}
					}
				}

				stage('Checkout CloudDeployment Automation project') {
					agent {
						label 'ISUM'
					}
					steps {
						script {
							dir('/opt/install'){
								sh 'git clone -b dev --recursive https://github.com/AbhishekGupta1506/CloudTransformCICD.git'
								checkout([$class: 'SubversionSCM', locations: [[credentialsId: 'abgWC', local: '.', remote: 'http://svndae.hq.sag:1818/svn/sag/integration-live/installation/trunk/']]])
								sh 'chmod 777 *'
								//remove once automation is fixed from WCIC team
								sh 'cp /opt/install/CloudTransformCICD/CloudDeploymentAssets/ISUM/gradle.properties /opt/install'
								sh 'cp /opt/install/CloudTransformCICD/CloudDeploymentAssets/ISUM/um.txt /opt/install/templates/um.txt'
							}
						}
					}
				}

				/*stage('checkout designer'){
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
				}*/
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
							sh './gradlew -b download.gradle download'
							sh './gradlew installCTP -x validate'

							sh './gradlew installIntegrationWar -x validate'

							sh './gradlew customizeCTP -x validate'							
						}
					}
				}

				stage ('Run MySql script on cloud setup'){
					agent{label 'MySQL'}
					steps {
						sh '/etc/init.d/mysql stop'
						sh 'mysqld --defaults-file=/usr/my-ipaas.ini -u root 2>1 &'

						dir('/opt/install'){
							//sh './gradlew -b download.gradle download' 
							sh './gradlew executeDatabaseScripts -x validate'
							sh './gradlew executeCustomSQLScripts -x validate'
							sh './gradlew apply_10_7_patch -x validate'
							sh './gradlew apply_10_8_patch -x validate'
							sh './gradlew executeDatabasePatchScripts -x validate'
							sh './gradlew customizeDB -x validate'
						}
							sh 'service nginx stop'
							sh 'service nginx start'
					}
				}

				stage ('Install IS+UM on cloud setup'){
					agent{label 'ISUM'}
					steps {
						sh 'service nginx stop'

						sh 'service nginx start'

						dir('/opt/install'){
							sh './gradlew installIS -x validate'
							sh './gradlew installUM -x validate'
						}
					}
				}
				/*stage('Installing the Designer') {

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
				}*/

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