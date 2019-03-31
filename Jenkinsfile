pipeline {

  agent any

  options {
    buildDiscarder(
      logRotator(numToKeepStr: '10', artifactNumToKeepStr: '5')
    )
  }

  stages {
    stage('Build and test') {
      agent {
        docker {
          image 'hseeberger/scala-sbt'
          args '-v /srv/jenkins/.sbt:/tmp/.sbt -v /srv/jenkins/.ivy2:/tmp/.ivy2'
          reuseNode true
        }
      }

      steps {
        sh "echo 'remove any old artifacts from workspace'"
        sh "rm *.jar || echo 'nothing to remove here'"
        checkout scm
        sh "echo Setting Version to `echo $BRANCH_NAME-$BUILD_NUMBER | tr / _`"
        sh "sed -i -e 's~version := .*~version := \"'`echo $BRANCH_NAME-$BUILD_NUMBER | tr / _`'\"~g' build.sbt"

        echo "Running Tests.."

        
        sh '''sbt -Dsbt.global.base=/tmp/.sbt \\
                  -Dsbt.boot.directory=/tmp/.sbt/boot \\
                  -Dsbt.ivy.home=/tmp/.ivy2 \\
              clean test'''


        stash name:'stash', includes:'**'
      }
    }
  }
}
