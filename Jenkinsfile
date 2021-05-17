#!groovy

awsCredentialsId = 'aws.creds'
awsRegion = 'us-west-2'
ecrUrl = 'https://407487714479.dkr.ecr.us-west-2.amazonaws.com'
docker_image_name = 'spring-boot-actuator'

node('master') {
	
	try {

		stage('Checkout') {
			checkout()

		}
		stage('Build Code') {
             build()   
         }
		stage('Push ECR')
		{
			deployToECR()
		}

		if ( params.buildType == deploy || buildDeploy ){

			stage("EKS Deploy") {
				//deploy()

			}			
		}


} catch (Exception e) {
                println("Caught exception: " + e)
                error = catchException exception: e
            } finally {
                println("CurrentBuild result: " + currentBuild.result)
                Notify()
            }

 }


def build() {
	sh "mvn clean install -skipDTests"
	sh "cp /target/spring-boot-0.0.1-SNAPSHOT.jar	infra/"
}

def deployToECR(){
dir('infra/') {
withCredentials([[$class: 'UsernamePasswordMultiBinding', credentialsId: awsCredentialsId, usernameVariable: 'ACCESS_KEY', passwordVariable: 'SECRET_KEY']]) {
docker.withRegistry(ecrUrl, awsCredentialsId){
	def login_cmd = sh(script: "AWS_ACCESS_KEY_ID=${ACCESS_KEY} AWS_SECRET_ACCESS_KEY=${SECRET_KEY} AWS_DEFAULT_REGION=${awsRegion} aws ecr get-login --no-include-email", returnStdout: true)
	sh "#!/bin/sh -e\n ${login_cmd}"
    new_image = docker.build(docker_image_name)
    new_image.push('latest')
    new_image.push("${tagName}")
			}
		}
	}
}


def Notify(){

	// CW events to and Have a SNS Topic to send EMAIL 
	// <OR> 
	// SLACK INTEGRAGTION simple curl -X POST to specific channel with buildInfo.. 
}


def checkout() {
    checkout scm
    def commit = sh(returnStdout: true, script: 'git rev-parse HEAD').trim()
    env.GIT_COMMIT = commit
}
