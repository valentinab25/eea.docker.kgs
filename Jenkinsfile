pipeline {
  agent any

  environment {
     GIT_ORG = "valentinab25"
     GIT_NAME = "eea.docker.kgs"
     EXCLUDE = 'eea.google'
  }

  stages {
    stage('BuildTest') {
      steps {
        node(label: 'docker-1.13') {
        sh '''env'''
        }
      }
    }

    stage('Release') {
      when {
        allOf {
          environment name: 'CHANGE_ID', value: ''
          branch 'master'
        }
      }
      steps {
        node(label: 'docker-1.13') {
          withCredentials([string(credentialsId: 'TestNewhook', variable: 'GITHUB_TOKEN')]) {
            sh '''docker run -i --rm --name="$BUILD_TAG-nightlyrelease" -e GIT_BRANCH="$BRANCH_NAME" -e GIT_NAME="$GIT_NAME" -e GIT_VERSIONFILE="$GIT_VERSIONFILE" -e DOCKERHUB_REPO=valentinab25/eea.docker.kgs -e GIT_ORG="$GIT_ORG" -e GIT_TOKEN="$GITHUB_TOKEN" gitflow'''
          }
        }
      }
    }
  }

  post {
    changed {
      script {
        def url = "${env.BUILD_URL}/display/redirect"
        def status = currentBuild.currentResult
        def subject = "${status}: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]'"
        def summary = "${subject} (${url})"
        def details = """<h1>${env.JOB_NAME} - Build #${env.BUILD_NUMBER} - ${status}</h1>
                         <p>Check console output at <a href="${url}">${env.JOB_BASE_NAME} - #${env.BUILD_NUMBER}</a></p>
                      """

        def color = '#FFFF00'
        if (status == 'SUCCESS') {
          color = '#00FF00'
        } else if (status == 'FAILURE') {
          color = '#FF0000'
        }
        slackSend (color: color, message: summary)
        emailext (subject: '$DEFAULT_SUBJECT', to: '$DEFAULT_RECIPIENTS', body: details)
      }
    }
  }
}
