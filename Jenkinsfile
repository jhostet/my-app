pipeline {
  agent {label 'linux'}
  options {
    buildDiscarder(logRotator(numToKeepStr: '10', artifactNumToKeepStr: '4'))
  }
  stages {
    stage('Release') {
      when {
        beforeAgent true
        not { changelog '.*maven-release-plugin.*' }
      }
      tools {
        jfrog 'jfrog-cli-latest'
      }
      environment {
        JFROG_CLI_LOG_LEVEL="DEBUG"
      }
      steps {
        sh 'git tag | xargs git tag -d'
        withMaven(jdk: 'openjdk-21', maven: 'default', mavenSettingsConfig: 'jfrog-maven-settings', traceability: true) {
          jf "mvnc --server-id-deploy jfrog-cloud --server-id-resolve jfrog-cloud --repo-deploy-snapshots maven-snapshot-local --repo-deploy-releases maven-release-local --repo-resolve-snapshots maven-snapshot-local --repo-resolve-releases maven-release-local"
          jf "mvn clean package"
          jf "mvn -B release:prepare -Dresume=false -DpushChanges=false"
          jf 'mvn release:perform -Dgoals="install" -DlocalCheckout=true'
          jf 'mvn install'
        }
      }
    }
  }
}
