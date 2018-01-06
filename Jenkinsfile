properties(
	[
		buildDiscarder(logRotator(artifactDaysToKeepStr: '', artifactNumToKeepStr: '', daysToKeepStr: '', numToKeepStr: '5')),
		pipelineTriggers([pollSCM('0 H(5-6) * * *')])
	]
)

pipeline
{
	agent { node { label 'linux && development' } }
	
	stages
	{
		stage('Checkout')
		{
			steps
			{
				//cleanWs()
				dir('src/github.com/influxdata/telegraf') 
				{
					checkout scm
				}
				sh '''
					rm -f -d -r ./src/github.com/influxdata/telegraf/build'''
			}
		}
		
		stage('Build') 
		{
			steps
			{
				sh '''
					export GOROOT=/usr/local/go
					export PATH=$PATH:$GOROOT/bin
					export GOPATH=${WORKSPACE}
					workspace=`pwd`
					cd ./src/github.com/influxdata/telegraf
					make package'''
			}
		}
		
		stage('CleanUp')
		{
			steps
			{
				//cleanWs()
				notifySuccessful()
			}
		}
	}
	post 
	{ 
        failure { 
            notifyFailed()
        }
		success { 
            notifySuccessful()
        }
		unstable { 
            notifyFailed()
        }
    }
}

def notifySuccessful() {
	echo 'Sending e-mail'
	mail (to: 'notifier@manobit.com',
         subject: "Job '${env.JOB_NAME}' (${env.BUILD_NUMBER}) success build",
         body: "Please go to ${env.BUILD_URL}.");
}

def notifyFailed() {
	echo 'Sending e-mail'
	mail (to: 'notifier@manobit.com',
         subject: "Job '${env.JOB_NAME}' (${env.BUILD_NUMBER}) failure",
         body: "Please go to ${env.BUILD_URL}.");
}