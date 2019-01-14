void setBuildStatus(String message, String state) {
    step([
        $class: "GitHubCommitStatusSetter",
        reposSource: [$class: "ManuallyEnteredRepositorySource", url: "https://github.com/JinChengShi/test"],
        contextSource: [$class: "ManuallyEnteredCommitContextSource", context: "ci/jenkins/test"],
        errorHandlers: [[$class: "ChangingBuildStatusErrorHandler", result: "UNSTABLE"]],
        statusResultSource: [$class: "ConditionalStatusResultSource", results: [[$class: "AnyBuildResult", message: message, state: state]]]
    ]);
}
pipeline {
    agent any
    options {
        disableConcurrentBuilds()
        buildDiscarder(logRotator(numToKeepStr:"5"))
        timeout(time: 40, unit: "MINUTES")
    }
    stages {
        stage("BuildAndEnv"){
            parallel {
                stage("build") {
                    steps {
                        echo "$GIT_COMMIT"
                        sh "git --no-pager show -s --format='%an' ${GIT_COMMIT}"
                        sh "git --no-pager show -s --format='%ae' ${GIT_COMMIT}"
                        sh 'env' 
                    }
                }
            }
        }
    }
    post {
		success {
			script {
				//setBuildStatus("Build succeeded", "SUCCESS");
				def comment = pullRequest.comment('This PR 11111111111')
				pullRequest.createStatus(status: 'success',
                         				context: 'ci/jenkins/test',
                         				description: 'All tests are passing',
                         				targetUrl: "${env.JOB_URL}/testResults")
			}
		}
	}
}
