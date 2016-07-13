#!groovy

/* Only keep the 10 most recent builds. */
properties([[$class: 'BuildDiscarderProperty',
                strategy: [$class: 'LogRotator', numToKeepStr: '10']]])

node {
  stage 'Checkout'
  checkout scm

  stage 'Build'

  /* Call the ant build. */
  ant "info"

  stage 'Verify'
  println "JOB_NAME is '${env.JOB_NAME}'"
  println "BUILD_NUMBER is '${env.BUILD_NUMBER}'"
  String jobName = "${env.JOB_NAME}" // bugs/JENKINS-36419-multi-branch-pipeline-AbstractMethodError/JENKINS-36637
  String jobPath = "job/" + jobName.replace("/", "/job/") + "/"
  println "job path is '${jobPath}'"
  String buildNumber = "${currentBuild.number}"
  println "build number is '${buildNumber}'"
  String jobURL = "http://localhost:8080/${jobPath}/${buildNumber}/api/xml?wrapper=changes&xpath=//changeSet//comment"
  println "job URL is '${jobURL}'"
  String changesURL = "http://localhost:8080/job/bugs/job/JENKINS-36637-changes-list-incomplete/${currentBuild.number}/api/xml?wrapper=changes&xpath=//changeSet//comment"
  String changeDescription =
    new URL(changesURL).getText(connectTimeout: 1000,
                                readTimeout: 5000,
                                useCaches: false,
                                allowUserInteraction: false,
                                requestProperties: ['Connection': 'close'])
  println "Change URL is '" + changesURL + "'"
  println "Change description is '" + changeDescription + "'"
  if (changeDescription.contains("<changes/>") ||
      !changeDescription.contains("<changes>")) {
    manager.addWarningBadge("Missing recent changes output")
    manager.createSummary("warning.gif").appendText("<h1>Missing recent changes!</h1>", false, false, false, "red")
    manager.buildUnstable()
  }
}

/* Run ant from tool "ant-latest" */
void ant(def args) {
  /* Get jdk tool. */
  String jdktool = tool name: "jdk8", type: 'hudson.model.JDK'

  /* Get the ant tool. */
  def antHome = tool name: 'ant-latest', type: 'hudson.tasks.Ant$AntInstallation'

  /* Set JAVA_HOME, and special PATH variables. */
  List javaEnv = [
    "PATH+JDK=${jdktool}/bin", "JAVA_HOME=${jdktool}", "ANT_HOME=${antHome}",
  ]

  /* Call ant tool with java envVars. */
  withEnv(javaEnv) {
    if (isUnix()) {
      sh "${antHome}/bin/ant ${args}"
    } else {
      bat "${antHome}\\bin\\ant ${args}"
    }
  }
}
