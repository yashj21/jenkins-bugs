#!groovy

@Library('globalPipelineLibraryMarkEWaite@branch-for-checkout-in-library') _
import com.markwaite.Assert
import com.markwaite.Build
import com.markwaite.Checkout

/* Only keep the 10 most recent builds. */
properties([[$class: 'BuildDiscarderProperty',
                strategy: [$class: 'LogRotator', numToKeepStr: '10']]])

node {
  stage('Checkout') {
    // JENKINS-50394 reports missing object exception during branch indexing
    def my_checkout = new com.markwaite.Checkout()
    my_checkout.checkoutBranch('JENKINS-50394')
  }

  stage('Build') {
    /* Call the ant build. */
    def my_step = new com.markwaite.Build()
    my_step.ant 'info'
  }

  stage('Verify') {
    def my_check = new com.markwaite.Assert()
    if (currentBuild.number > 1) { // Don't check first build
      my_check.logContains('.*Author:.*', 'Build started without a commit - no author line')
      my_check.logContains('.*Date:.*', 'Build started without a commit - no date line')
    }
  }
}
