pipeline {
  agent any

  tools {
    maven 'Maven 3.9'
    jdk 'Java 17'
  }

  options {
    buildDiscarder logRotator(
      artifactDaysToKeepStr: '',
      artifactNumToKeepStr: '',
      daysToKeepStr: '1',
      numToKeepStr: '5'
    )
  }

  stages {
    stage('Build') {
      steps {
        sh 'mvn -B clean package -DskipTests'
      }
    }

    stage('Policy') {
      steps {
        script {
          nexusPolicyEvaluation(
            enableDebugLogging: false,
            iqStage: 'build',
            iqApplication: 'sandbox-application',
            failBuildOnNetworkError: true,
            iqScanPatterns: [
              [scanPattern: '**/target/*.jar']
            ],
            reachability: [
              logLevel: 'DEBUG',
              javaAnalysis: [
                enable: true,
                entrypointStrategy: 'JAVA_MAIN',
                namespaces: [
                  [namespace: 'org.owasp.webgoat']
                ]
              ]
            ]
          )

          archiveArtifacts(
            artifacts: '**/bomxray.log',
            followSymlinks: false
          )
        }
      }
    }
  }
}
