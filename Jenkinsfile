pipeline {

  agent none 
  stages {
		stage('Install products') {
			parallel {


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
					agent { label 'ISUM' }
					steps {
						echo "hello IS + UM"
					}
				}

			}
	  }

		//stage('Run tests') {
			//agent{
			//	label 'OnPremDesigner'
			//}
			//steps {
			//	echo "Run the the test cases here"
			//}
		//}
  }
}