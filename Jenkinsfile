properties(
	[
		buildDiscarder(logRotator(artifactDaysToKeepStr: '', artifactNumToKeepStr: '', daysToKeepStr: '', numToKeepStr: '5')),
		pipelineTriggers([upstream(threshold: 'SUCCESS', upstreamProjects: 'Marianob85/telegraf/HuaweiHilinkApi, Marianob85/telegraf/OpenHardwareMonitor '), pollSCM('0 H(5-6) * * *')])
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
				dir('src/github.com/influxdata/telegraf') 
				{
					checkout changelog: true, poll: true, scm: [$class: 'GitSCM', branches: [[name: '*/master']], doGenerateSubmoduleConfigurations: false, extensions: [[$class: 'PreBuildMerge', options: [fastForwardMode: 'FF', mergeRemote: 'origin', mergeTarget: 'HuaweiHilinkApi']], [$class: 'PreBuildMerge', options: [fastForwardMode: 'FF', mergeRemote: 'origin', mergeTarget: 'OpenHardwareMonitor']]], submoduleCfg: [], userRemoteConfigs: [[credentialsId: '5f43e7cc-565c-4d25-adb7-f1f70e87f206', url: 'https://github.com/marianob85/telegraf']]]
				}
				sh '''
					rm -f -d -r ./release
					rm -f -d -r ./src/github.com/influxdata/telegraf/build
					mkdir ./release'''
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
					make package
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
