#!groovy

node("linux") {
	ansiColor("xterm") {
		stage ("Checkout") {
			deleteDir()
			checkout([$class: 'GitSCM', branches: [[name: '*/master']], doGenerateSubmoduleConfigurations: false, extensions: [], submoduleCfg: [], userRemoteConfigs: [[url: 'https://github.com/NativeScript/nativescript-vscode-extension/']]])
		}

		docker.image("mkenney/docker-npm").inside() {	
			stage ("Install dependencies") {
				withEnv([npmLogLevelEnvVar]) {
					sh "npm install"
				}
			}

			stage ("Build and package") {
				withEnv([npmLogLevelEnvVar]) {
					sh "npm run build"
					sh "npm run package"
				}
			}

			stage ("Archive Artifacts") {
				bat "git rev-parse HEAD > git-commit-id.txt"
				currentBuildCommitId = readFile("git-commit-id.txt").trim()
				archiveArtifacts artifacts: "**/*.vsix, git-commit-id.txt"
			}
		}
	}
}