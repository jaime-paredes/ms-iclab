
def last_stage

pipeline {
    agent any
    tools {
        maven 'maven'
    }
    stages {

      stage("SonarQube") {
        when{
          expression{
            "${env.BRANCH_NAME}" != "master"
          }
        }
        steps {
          script {
            last_stage = env.STAGE_NAME
          }            
          withSonarQubeEnv(credentialsId: "access_token_sq", installationName: "MySonar") {
            sh "mvn org.sonarsource.scanner.maven:sonar-maven-plugin:3.7.0.1746:sonar -Dsonar.target=sonar.java.binaries"
          }
        }
      }

      stage("Maven build") {
        when{
          expression{
            "${env.BRANCH_NAME}" != "master"
          }
        }
        steps{
          echo "Maven build"
          script {
            last_stage = env.STAGE_NAME
            sh "mvn clean package"
          }
        }
      }

      stage("GIT Tag") {
        when{
          expression{
            "${env.BRANCH_NAME}" ==~ /release\/.*/
          }
        }
        steps{
          echo "GIT Tag"
          script {
            last_stage = env.STAGE_NAME
            sh 'mvn -B -Darguments="-Dmaven.test.skip=true -Dmaven.deploy.skip=true" -DtagNameFormat="V@{project.version}" -DgitRepositoryUrl=git@github.com:jaime-paredes/ms-iclab.git -Dresume=false -DscmCommentPrefix="[skip ci]" -DpushChanges=false release:prepare release:perform'
          }
        }
      }
    }   
}
