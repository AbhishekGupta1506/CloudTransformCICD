pipeline {
    agent none
	//agent { label 'master'}
    options {
        buildDiscarder(logRotator(numToKeepStr:'10'))
        disableConcurrentBuilds()
        skipDefaultCheckout()
    }

	/*tools {
        jdk 'jdk-1.8'
    }*/

    stages {

		stage('Restart VMs') {
					agent {
						label 'master'
					}
					steps{
							sh 'echo $JAVA_HOME'
							sh 'java -version'
							/*vSphere buildStep: [$class: 'PowerOff', evenIfSuspended: false, shutdownGracefully: false, vm: 'VMISPRO01'], serverName: 'vsphere_cloud'
     						vSphere buildStep: [$class: 'PowerOff', evenIfSuspended: false, shutdownGracefully: false, vm: 'VMBLRPT05'], serverName: 'vsphere_cloud'
							vSphere buildStep: [$class: 'PowerOff', evenIfSuspended: false, shutdownGracefully: false, vm: 'VMISPRO03'], serverName: 'vsphere_cloud'*/
							vSphere buildStep: [$class: 'PowerOff', evenIfSuspended: false, shutdownGracefully: false, vm: 'VMSIQACLOUD02'], serverName: 'vsphere_cloud'
							sleep 10
							/*vSphere buildStep: [$class: 'PowerOn', timeoutInSeconds: 180, vm: 'VMISPRO01'], serverName: 'vsphere_cloud'
							vSphere buildStep: [$class: 'PowerOn', timeoutInSeconds: 180, vm: 'VMBLRPT05'], serverName: 'vsphere_cloud'
							vSphere buildStep: [$class: 'PowerOn', timeoutInSeconds: 180, vm: 'VMISPRO03'], serverName: 'vsphere_cloud'*/
							vSphere buildStep: [$class: 'PowerOn', timeoutInSeconds: 180, vm: 'VMSIQACLOUD02'], serverName: 'vsphere_cloud'
							sleep 180
					}
		}
		stage('Checkout jobs'){
			parallel{

				/*stage('Checkout CloudDeployment Automation project in CTP') {
					agent {
						label 'CTP'
					}
					steps {
						script {
							dir('/opt/install'){
								sh 'git clone -b test --recursive https://github.com/AbhishekGupta1506/CloudTransformCICD.git'
								checkout([$class: 'SubversionSCM', locations: [[credentialsId: 'abgWC', local: '.', remote: 'http://svndae.hq.sag:1818/svn/sag/integration-live/installation/trunk/']]])
								sh 'chmod 777 *'
								//remove once automation is fixed from WCIC team
								sh 'cp /opt/install/CloudTransformCICD/CloudDeploymentAssets/CTP/gradle.properties /opt/install'
							}
						}
					}
				}

				stage('Checkout CloudDeployment Automation project in MySQL') {
					agent {
						label 'MySQL'
					}
					steps {
						script {
							dir('/opt/install'){
								sh 'git clone -b test --recursive https://github.com/AbhishekGupta1506/CloudTransformCICD.git'
								checkout([$class: 'SubversionSCM', locations: [[credentialsId: 'abgWC', local: '.', remote: 'http://svndae.hq.sag:1818/svn/sag/integration-live/installation/trunk/']]])
								sh 'chmod 777 *'
								//remove once automation is fixed from WCIC team
								sh 'cp /opt/install/CloudTransformCICD/CloudDeploymentAssets/SQL/gradle.properties /opt/install'
							}
						}
					}
				}

				stage('Checkout CloudDeployment Automation project in ISUM') {
					agent {
						label 'ISUM'
					}
					steps {
						script {
							dir('/opt/install'){
								sh 'git clone -b test --recursive https://github.com/AbhishekGupta1506/CloudTransformCICD.git'
								checkout([$class: 'SubversionSCM', locations: [[credentialsId: 'abgWC', local: '.', remote: 'http://svndae.hq.sag:1818/svn/sag/integration-live/installation/trunk/']]])
								sh 'chmod 777 *'
								//remove once automation is fixed from WCIC team
								sh 'cp /opt/install/CloudTransformCICD/CloudDeploymentAssets/ISUM/gradle.properties /opt/install'
								sh 'cp /opt/install/CloudTransformCICD/CloudDeploymentAssets/ISUM/um.txt /opt/install/templates/um.txt'
							}
						}
					}
				}*/

				stage('checkout designer'){
					agent{
						label 'Designer'
					}
					steps{
						script{
							dir('C:/CloudCheckOut'){
								echo "Started: checking out the GIT project in designer setup"
								bat 'git clone -b test --recursive https://github.com/AbhishekGupta1506/CloudTransformCICD.git'
								echo "Completed: checking out the GIT project in designer setup"
							}
						}
					}
				}
				stage('checkout CCE project'){
					agent{
						label 'Designer'
					}
					steps{
						script{
							dir('C:/CloudCheckOut'){
								echo "Started: checking out the CCE GIT project in on-premise setup"
								bat 'git clone --recursive http://irepo.eur.ad.sag/scm/devops/command-central.git'
								echo "Completed: checking out the CCE GIT project in on-premise setup"
							}
						}
					}
				}
			}
		}
		
		stage('Install products') {
			parallel {
				
				/*stage ('Install CTP on cloud setup'){
					agent{label 'CTP'}
					steps {
						sh 'postfix stop'
						sh 'postfix start'
						sh 'memcached -d -u root -m 256'
						dir('/opt/install'){
							withEnv(['PATH+JAVA_HOME=/home/svtuser/jdk1.8.0_131/bin']) {
          					echo "PATH is: $PATH"
							sh './gradlew -b download.gradle download'
							sh './gradlew installCTP -x validate'

							sh './gradlew installIntegrationWar -x validate'

							sh './gradlew customizeCTP -x validate'	
							}						
						}
					}
				}

				stage ('Run MySql script on cloud setup'){
					agent{label 'MySQL'}
					steps {
						//sh '/etc/init.d/mysql stop'
						//sh 'mysqld --defaults-file=/usr/my-ipaas.ini -u root 2>1 &'

						dir('/opt/install'){
							withEnv(['PATH+JAVA_HOME=/home/svtuser/jdk1.8.0_131/bin']) {
							echo "PATH is: $PATH"
							sh './gradlew -b download.gradle download' 
							sh './gradlew executeDatabaseScripts -x validate'
							sh './gradlew executeCustomSQLScripts -x validate'
							sh './gradlew apply_10_7_patch -x validate'
							sh './gradlew apply_10_8_patch -x validate'
							sh './gradlew executeDatabasePatchScripts -x validate'
							sh './gradlew customizeDB -x validate'
							}
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
							withEnv(['PATH+JAVA_HOME=/home/local/EUR/siqavm/jdk1.8.0_131/bin']) {
							sh './gradlew -b download.gradle download' 
							sh './gradlew installIS -x validate'
							//sh './gradlew installUM -x validate'
							}
						}
					}
				}*/
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

				stage('Migrate On-Premise 912 to 10.3') {

					agent {
						label 'Designer'
					}
					steps{
						script{
							dir('C:/CloudCheckOut/command-central'){
								echo "Start: CCE installation"
								bat 'ant boot -Dbootstrap=blr'
								echo "Completed: CCE installation"
							}
							echo "Start: Migrate On-Premise 912 to 10.3"
							bat 'mkdir C:/Users/Administrator/sag/cc/profiles/CCE/data/templates/composite/sag-ic-migration'
							bat 'copy C:/CloudCheckOut/CloudTransformCICD/CCE/ C:/Users/Administrator/sag/cc/profiles/CCE/data/templates/composite/sag-ic-migration/'
							dir('C:/Users/Administrator/sag/cc/profiles/CCE/bin'){
								bat 'sagcc exec templates composite apply sag-ic-migration -i C:/Users/Administrator/sag/cc/profiles/CCE/data/templates/composite/sag-ic-migration/env.properties'
							}
							echo "Completed: Migrate On-Premise 912 to 10.3"
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
						mail to: 'abg@softwareag.com, mjan@softwareag.com, mosy@softwareag.com, meea@softwareag.com, inra@softwareag.com',
						//mail to: 'abg@softwareag.com',
						subject: "CloudTransformation Pipeline completed -- JobName: ${env.JOB_NAME} BuildNumber: ${env.BUILD_NUMBER}",
						body: "checkout the report: ${env.BUILD_URL}"
			}
	}
}