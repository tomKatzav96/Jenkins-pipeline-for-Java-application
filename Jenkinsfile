      pipeline {
        agent any
        environment {
          CI = true
          ARTIFACTORY_ACCESS_TOKEN = credentials('artifactory-access-token')
        }
        stages {
          stage("build & SonarQube analysis") {
            steps {
              withSonarQubeEnv(installationName: 'sq1') {
                sh 'mvn clean install sonar:sonar'
              }
            }
          }
          stage("Quality Gate") {
            steps {
              timeout(time: 1, unit: 'MINUTES') {
                waitForQualityGate abortPipeline: true
              }
            }
          }
          stage("Upload to Artifactory") {
            agent {
              docker {
                image 'releases-docker.jfrog.io/jfrog/jfrog-cli-v2:2.2.0'
                reuseNode true
              }
            }
            steps {
              sh 'jfrog rt upload --url http://13.38.90.13:8082/artifactory/ --access-token ${ARTIFACTORY_ACCESS_TOKEN} target/Calculator-1.0-SNAPSHOT.jar java-calculator/'
            }
          }
        }
      }