pipeline {
	agent {
	    //label 'fargate-workers'
        ecs {
            inheritFrom 'fargate-workers'
            image '762845314743.dkr.ecr.eu-central-1.amazonaws.com/samplejenkinsonawsecs/workers/jenkinsexample/ubuntu1404:latest'
            //cpu 2048
            //memory 4096
            //logDriver 'fluentd'
            //logDriverOptions([[name: 'foo', value:'bar'], [name: 'bar', value: 'foo']])
            //portMappings([[containerPort: 22, hostPort: 22, protocol: 'tcp'], [containerPort: 443, hostPort: 443, protocol: 'tcp']])
        }
	}

	options {
		buildDiscarder(logRotator(numToKeepStr: '10'))
	}

	parameters {
		booleanParam name: 'RUN_TESTS', defaultValue: true, description: 'Run Tests?'
		booleanParam name: 'RUN_ANALYSIS', defaultValue: true, description: 'Run Static Code Analysis?'
		booleanParam name: 'DEPLOY', defaultValue: true, description: 'Deploy Artifacts?'
	}

	stages {
        stage('Build') {
            steps {
                cmake arguments: '-DCMAKE_TOOLCHAIN_FILE=~/Projects/vcpkg/scripts/buildsystems/vcpkg.cmake', installation: 'InSearchPath'
                cmakeBuild buildType: 'Release', cleanBuild: true, installation: 'InSearchPath', steps: [[withCmake: true]]
            }
        }

        stage('Test') {
            when {
                environment name: 'RUN_TESTS', value: 'true'
            }
            steps {
                ctest 'InSearchPath'
            }
        }

        stage('Analyse') {
            when {
                environment name: 'RUN_ANALYSIS', value: 'true'
            }
            steps {
                sh label: '', returnStatus: true, script: 'cppcheck . --xml --language=c++ --suppressions-list=suppressions.txt 2> cppcheck-result.xml'
                publishCppcheck allowNoReport: true, ignoreBlankFiles: true, pattern: '**/cppcheck-result.xml'
            }
        }

        stage('Deploy') {
            when {
                environment name: 'DEPLOY', value: 'true'
            }
            steps {
                sh label: '', returnStatus: true, script: '''cp jenkinsexample ~
                cp test/testPro ~'''
            }
        }
	}
}
