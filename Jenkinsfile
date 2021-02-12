pipeline {
  agent any
  options {
    lock resource: 'htmlunit-test-ports'
    disableConcurrentBuilds()
    timestamps()
    timeout(time: 140, unit: 'MINUTES')
  }
  tools {
    jdk 'openjdk-1.8'
  }
  stages {
    stage('Build') {
      steps {
        sh 'mvn clean verify spotbugs:spotbugs checkstyle:checkstyle pmd:pmd pmd:cpd javadoc:javadoc -U -P without-library-and-huge-tests -Dgpg.skip -Djava.awt.headless=true'
      }
    }
  }
  post {
    always {
      junit allowEmptyResults: true, testResults: 'deploy/junit/*.xml'
      recordIssues enabledForFailure: true, sourceCodeEncoding: 'UTF-8', sourceDirectory: 'src', tools: [
        checkStyle(),
        spotBugs(),
        pmdParser(),
        cpd(),
        java(),
        javaDoc()]
      step([$class: 'Mailer',
        notifyEveryUnstableBuild: true,
        recipients: "rbri@rbri.de"])
      }
    }
}