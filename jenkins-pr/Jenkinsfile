pipeline {
    agent any
    tools {
        go 'go1.18'
    }
    environment {
        GO114MODULE = 'on'
        CGO_ENABLED = 0
    }
    stages {
		stage('Checkout') {
			steps {
				checkout([
					$class: 'GitSCM',
					branches: [[name: '*/${GIT_BRANCH}']],
					clean: true,
					extensions: [],
					submoduleCfg: [],
					userRemoteConfigs: [[
					name: 'origin',
					refspec: '+refs/pull/${ghprbPullId}/*:refs/remotes/origin/pr/${ghprbPullId}/*',
					url: '${ghprbAuthorRepoGitUrl}'
					]]
				])
			}
		}
		stage('Code Analysis') {            
			steps {				
                withEnv(["PATH+GO=${HOME}/go/bin"]){
                    sh 'go install github.com/golangci/golangci-lint/cmd/golangci-lint@v1.46.2'
                    sh 'make lint'
                }
            }
		}
        stage("Unit Test") {
            steps {
                sh 'make test'
            }
        }
    }
	post {
        always {
            cleanWs()
        }
    }
}