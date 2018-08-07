pipeline {
	agent none
	stages {
		stage('Run Tests') {
			parallel {
				stage('Test On Windows') {
					agent { label "windows" }
					steps {
					echo "run-tests.bat"
					}
				}
				stage('Test On Linux') {
					agent { label "linux" }
					steps {
					echo "run-tests.sh"
					}
				}
			}
		}
	}
}