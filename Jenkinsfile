#!groovy

awsCredentialsId = 'aws.creds1'
awsRegion = 'us-west-2'
ecrUrl = 'https://407487714479.dkr.ecr.us-west-2.amazonaws.com'
url = '407487714479.dkr.ecr.us-west-2.amazonaws.com'
docker_image_name = 'spring-boot-actuator'
tagName = 'latest'

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

		stage("EKS Deploy") {
			deploy()
		}			

} catch (Exception e) {
                println("Caught exception: " + e)
                currentBuild.result = hudson.model.Result.FAILURE.toString()
               // error = catchException exception: e
            } finally {
                println("CurrentBuild result: " + currentBuild.result)
                Notify() 
            }

 }


def build() {
	sh "mvn clean install -DskipTests"
	sh "pwd && ls -a"
	sh "cp target/spring-boot-actuator-0.0.1-SNAPSHOT.jar infra/"
}


def deployToECR(){
dir('infra/') {
withCredentials([[$class: 'UsernamePasswordMultiBinding', credentialsId: awsCredentialsId, usernameVariable: 'ACCESS_KEY', passwordVariable: 'SECRET_KEY']]) {
 //docker.withRegistry('ecrUrl', 'ecr:us-west-2:awsCredentialsId'){ // Error with docker auth
	def login_cmd = sh(script: "AWS_ACCESS_KEY_ID=${ACCESS_KEY} AWS_SECRET_ACCESS_KEY=${SECRET_KEY} AWS_DEFAULT_REGION=${awsRegion} aws ecr get-login --no-include-email", returnStdout: true)
	sh "#!/bin/sh -e\n ${login_cmd}"
    new_image = docker.build(docker_image_name)
    sh "docker push ${url}/${docker_image_name}:${tagName}"
   // new_image.push("${url}:${tagName}")
//}
		}
	}
}


def deploy()
{
	println 'hello'
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
