pipeline {
  agent any

  stages {
    
    stage('Build') {
      steps {
        withGradle {
           sh './gradlew clean build --stacktrace -i'
        }
      }
    } // Build

    stage('Publish') {
      steps {
        withCredentials([usernamePassword(
        credentialsId: 'artifactory-creds', 
        passwordVariable: 'MVN_PASSWORD', 
        usernameVariable: 'MVN_USERNAME')]) {

          withGradle {
            sh """
              ./gradlew -i --stacktrace publish \
                  -PMVN_USERNAME=${MVN_USERNAME} \
                  -PMVN_PASSWORD=${MVN_PASSWORD} \
                  -PMVN_VERSION=1.${BUILD_NUMBER}
            """
          }  
        }
      }
    } // Publish

    stage('Post') {
      steps {
        script {
          jacoco()
          junit 'lib/build/test-results/test/*.xml'
          def pmd = scanForIssues tool: [$class: 'Pmd'], pattern: 'lib/build/reports/pmd/*.xml'
          publishIssues issues: [pmd]
        }
      }
    } // Post

  }
}
