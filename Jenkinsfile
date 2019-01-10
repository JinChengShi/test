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
                        echo "${CHANGE_AUTHOR_EMAIL}"
                        echo "${CHANGE_AUTHOR}"
                        echo "${CHANGE_AUTHOR_DISPLAY_NAME}"
                        GIT_NAME = $(git --no -pager show -s --format ='％a'$GIT_COMMIT)
                        GIT_EMAIL = $(git --no-pager show -s --format ='％ae'$GIT_COMMIT)
                        echo "$GIT_NAME"
                        echo "$GIT_EMAIL"
                    }
                }
            }
        }
    }
    post {
        always {
            echo "One way or another, I have finished"
        }
        success {
            setBuildStatus("Build succeeded", "SUCCESS");
            echo "I succeeeded!"
            mail to: "$env.mailtoUser",
            subject: "Success Pipeline: ${currentBuild.fullDisplayName}",
            body: "Success with ${env.BUILD_URL}"
        }
        unstable {
            echo "I am unstable :/"
            mail to: "$env.mailtoUser",
            subject: "Failed Pipeline: ${currentBuild.fullDisplayName}",
            body: "Something is wrong with ${env.BUILD_URL}"
        }
        failure {
            setBuildStatus("Build failed", "FAILURE");
            echo "I failed :("
            mail to: "$env.mailtoUser",
            subject: "Failed Pipeline: ${currentBuild.fullDisplayName}",
            body: "Something is wrong with ${env.BUILD_URL}"
        }
    }
}
