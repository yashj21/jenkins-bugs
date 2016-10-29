#!groovy

/* Only keep the 10 most recent builds. */
properties([[$class: 'BuildDiscarderProperty',
                strategy: [$class: 'LogRotator', numToKeepStr: '10']]])

node("master") {
  def check

  stage('Checkout') {
    fileLoader.withGit("${env.JENKINS_URL}/userContent.git", 'master', null, '') {
      check = fileLoader.load('pipelineChecks');
    }
    sh 'echo Working directory is `pwd`'
    sh 'pwd'
    sh 'echo JENKINS_HOME is $JENKINS_HOME' 
  }

  stage('Verify') {
    check.logContains(".*Working directory is ${env.JENKINS_HOME}.*", "Working dir report 1 missing")
    check.logContains("Working directory is ${env.JENKINS_HOME}.*", "Working dir report 2 missing")
    check.logContains("${env.JENKINS_HOME}.*", "Working dir report 3 missing")
    check.logContains("JENKINS_HOME is ${env.JENKINS_HOME}.*", "Working dir report 4 missing")
  }
}
