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
							vSphere buildStep: [$class: 'PowerOff', evenIfSuspended: false, shutdownGracefully: false, vm: 'VMISPRO03'], serverName: 'vsphere_cloud'
     						vSphere buildStep: [$class: 'PowerOff', evenIfSuspended: false, shutdownGracefully: false, vm: 'VMISPRO01'], serverName: 'vsphere_cloud'
							vSphere buildStep: [$class: 'PowerOff', evenIfSuspended: false, shutdownGracefully: false, vm: 'VMCCE02'], serverName: 'vsphere_cloud'
							vSphere buildStep: [$class: 'PowerOff', evenIfSuspended: false, shutdownGracefully: false, vm: 'VMSIQACLOUD02'], serverName: 'vsphere_cloud'
							sleep 10
							vSphere buildStep: [$class: 'PowerOn', timeoutInSeconds: 180, vm: 'VMISPRO03'], serverName: 'vsphere_cloud'
							vSphere buildStep: [$class: 'PowerOn', timeoutInSeconds: 180, vm: 'VMISPRO01'], serverName: 'vsphere_cloud'
							vSphere buildStep: [$class: 'PowerOn', timeoutInSeconds: 180, vm: 'VMCCE02'], serverName: 'vsphere_cloud'
							vSphere buildStep: [$class: 'PowerOn', timeoutInSeconds: 180, vm: 'VMSIQACLOUD02'], serverName: 'vsphere_cloud'
							sleep 180
					}
		}
		stage('Checkout jobs'){
			parallel{

				stage('Checkout CloudDeployment Automation project in CTP') {
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
								//sh 'git clone -b test --recursive https://github.com/AbhishekGupta1506/CloudTransformCICD.git'
								checkout([$class: 'SubversionSCM', locations: [[credentialsId: 'abgWC', local: '.', remote: 'http://svndae.hq.sag:1818/svn/sag/integration-live/installation/trunk/']]])
								sh 'git clone -b test --recursive https://github.com/AbhishekGupta1506/CloudTransformCICD.git'
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
						label 'DesignerWin'
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
						label 'DesignerWin'
					}
					steps{
						script{
							dir('C:/CloudCheckOut'){
								echo "Started: checking out the CCE GIT project in on-premise setup"
								bat 'git clone --recursive http://irepo.eur.ad.sag/scm/devops/command-central.git'
								echo "Completed: checking out the CCE GIT project in on-premise setup"
							}
							dir('C:/CloudCheckOut/command-central/cc-server'){
								//bat 'dir'
								bat 'rmdir antcc /s /q'
								//bat 'dir'
								bat 'git clone --recursive -b release/103oct2018 https://github.com/SoftwareAG/sagdevops-antcc.git antcc'
								//bat 'dir'
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
							withEnv(['PATH+JAVA_HOME=/home/svtuser/jdk1.8.0_131/bin']) {
          					//echo "PATH is: $PATH"
								sh 'iptables --flush'
								sh 'chmod -R 777 /opt/install'
								sh 'rm -rf /opt/install/os_independent/packages/.svn'
								sh './gradlew -b download.gradle download'
								sh 'postfix stop &'
								sh 'postfix start &'
								sh 'memcached -d -u root -m 256'
								sh 'chmod -R 777 /opt/install'
								sh './gradlew installCTP -x validate'
								sh '/opt/softwareag/profiles/CTP/bin/shutdown.sh'	
							}						
						}
					}
				}

				stage ('Run MySql script on cloud setup'){
					agent{label 'MySQL'}
					steps {
						dir('/opt/install'){
							withEnv(['PATH+JAVA_HOME=/usr/java/jdk1.8.0_172/bin']) {
							//echo "PATH is: $PATH"
								sh 'iptables --flush'
								sh 'chmod -R 777 /opt/install'
								sh 'rm -rf /root/.gradle'
								sh 'rm -rf /opt/install/os_independent/packages/.svn'
								sh './gradlew -b download.gradle download'
								sh './gradlew dropDatabases -x validate'
								sh 'rm -rf /root/.gradle'
								sh './gradlew -b download.gradle download'
								sh './gradlew installNginxPlus'
								sh './gradlew buildNginxConf'
								sh './gradlew deployNginxConf'
								sh 'cp config/server.key /usr/local/conf'
								sh 'cp config/server.crt /usr/local/conf'
								sh './gradlew executeDatabaseScripts -x validate'
								sh './gradlew executeCustomSQLScripts -x validate'
								sh './gradlew apply_10_7_patch -x validate'
								sh './gradlew apply_10_8_patch -x validate'
								sh './gradlew executeDatabasePatchScripts -x validate'
								sh 'rm -rf /opt/install/customize/SoftwareAG/artifacts/.svn'
								sh './gradlew customizeDB -x validate --stacktrace'
							}
						}
					}
				}

				stage ('Install IS+UM on cloud setup'){
					agent{label 'ISUM'}
					steps {
						dir('/opt/install'){
							withEnv(['PATH+JAVA_HOME=/home/svtuser/jdk1.8.0_131/bin']) {
								sh 'iptables --flush' 
								sh 'chmod -R 777 /opt/install'
								sh './gradlew -b download.gradle download'
								sh 'iptables --flush'
								sh 'rm -rf /opt/install/os_independent/packages/.svn'
								sh 'mkdir -p  /mnt-efs/wmic/upstream/default'
								sh 'mkdir -p  /mnt-efs/wmic/upstream/location'
								sh 'mkdir -p  /mnt-efs/wmic/upstream/container'
								sh './gradlew installNginxPlus'
								sh './gradlew installPHPFPM'
								sh './gradlew buildNginxConf'
								sh './gradlew deployNginxConf'
								sh 'chown -R nginx:nginx /mnt-efs'
								sh './gradlew installIS -x validate --stacktrace'
								sh 'iptables --flush'
							}
						}
					}
				}
				stage('Installing the Designer') {

					agent {
						label 'DesignerWin'
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
						label 'DesignerWin'
					}
					steps{
						script{
							dir('C:/CloudCheckOut/command-central'){
								echo "Start: CCE installation"
								bat 'ant boot -Dbootstrap=blr'
								bat 'ant up'
								echo "Completed: CCE installation"
							}
							echo "Start: Migrate On-Premise 912 to 10.3"
							bat 'mkdir C:\\Users\\Administrator\\sag\\cc\\profiles\\CCE\\data\\templates\\composite\\sag-ic-migration'
							bat 'copy C:\\CloudCheckOut\\CloudTransformCICD\\CCE\\ C:\\Users\\Administrator\\sag\\cc\\profiles\\CCE\\data\\templates\\composite\\sag-ic-migration\\'
							dir('C:/Users/Administrator/sag/cc/CommandCentral/client/bin'){
								bat 'sagcc exec templates composite apply sag-ic-migration -i C:/Users/Administrator/sag/cc/profiles/CCE/data/templates/composite/sag-ic-migration/env.properties -p manage'
							}
							echo "Completed: Migrate On-Premise 912 to 10.3"
						}
					}
				}

			}
	  }
	stage('Start servers ISUM') {
		agent {
			label 'ISUM'
		}
		steps {
			script {
				//dir('/opt/install'){
					sh 'iptables --flush'
					sh '/opt/softwareag/IntegrationServer/instances/default/bin/shutdown.sh'
					sh '/opt/softwareag/IntegrationServer/instances/default/bin/startup.sh'
					sh 'sleep 10m'
					/*
					status=0;
						until [ $status -eq 401 ]
						do
						status=$(curl -o /dev/null -u myself:XXXXXX -Isw '%{http_code}' http://localhost:5555)
							sleep 15
						done
					*/
					sh 'service php-fpm stop'
					sh 'service php-fpm start'
					sh 'service nginx stop'
					sh 'service nginx start'
				//}
			}
		}
	}
	stage('Start servers CTP') {
		agent {
			label 'CTP'
		}
		steps {
			script {
				//dir('/opt/install'){
					sh 'memcached -d -u root -m 256'
					sh '/opt/softwareag/profiles/CTP/bin/shutdown.sh'
					sh 'sleep 1m'
					sh 'iptables --flush'
					sh './gradlew installIntegrationWar -x validate'
					sh './gradlew customizeCTP -x validate'
					sh '/opt/softwareag/profiles/CTP/bin/startup.sh'
					sh 'sleep 10m'
				//}
			}
		}
	}

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