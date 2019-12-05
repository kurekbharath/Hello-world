pipeline {
    agent any
   
    stages {
        stage ('Build') {
            steps {
                echo 'This is a minimal pipeline.'
				bat 'D:\\apache-maven-3.6.3-bin\\apache-maven-3.6.3\\bin\\mvn package'
			}
        }
		stage ('upload') {
			steps {
				script {
				echo 'publishing artifacts to jfrog'
				zip dir: "${WORKSPACE}//target//my-app-1.0-SNAPSHOT.jar", glob: '', zipFile: "${WORKSPACE}//my-app-1.0-SNAPSHOT.zip"
				def VersionInputs = input message: "Please provide the  version to upload to JFROG",
				parameters: [string(defaultValue: '', description: 'Pass the version convention ', name: 'Version', trim: true)]
				env.Version	= VersionInputs
			
				echo "Upload version for : ${Version}"
		
				def server = Artifactory.server 'Artifactory'
				server.credentialsId = 'JFROGID'
					def uploadSpec = """{
						"files": [
							{
								"pattern": "${WORKSPACE}\my-app-1.0-SNAPSHOT.zip",
								"target": "example-repo-local\${Version}\",
								"props": "build.name=${Version};build.number= ${env.BUILD_NUMBER}",
								"flat": "true"
							}
						]
					}"""			
				def buildInfo = server.upload spec: uploadSpec
				buildInfo.name = "${Version}"
				buildInfo.number = "${env.BUILD_NUMBER}"
				server.publishBuildInfo buildInfo 
			}
			}
		}
	}
}