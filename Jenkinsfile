
def lastStage
def releaseVersion = "0.0.8"
def devVersion = "0.0.9-SNAPSHOT"

// Provide an optional comment prefix, e.g. for your bug tracking system
def scmCommentPrefix = 'Grupo-5: '

pipeline {
    agent any
    tools {
        maven 'maven'
    }
    stages {

      // stage("SonarQube") {
      //   when {
      //     expression{
      //       "${env.BRANCH_NAME}" != "master"
      //     }
      //   }
      //   steps {
      //     script {
      //       lastStage = env.STAGE_NAME
      //     }            
      //     withSonarQubeEnv(credentialsId: "access_token_sq", installationName: "MySonar") {
      //       sh "mvn org.sonarsource.scanner.maven:sonar-maven-plugin:3.7.0.1746:sonar -Dsonar.target=sonar.java.binaries"
      //     }
      //   }
      // }

      // stage("Maven build") {
      //   when {
      //     expression {
      //       "${env.BRANCH_NAME}" != "master"
      //     }
      //   }
      //   steps{
      //     echo "Maven build"
      //     script {
      //       lastStage = env.STAGE_NAME
      //       sh "mvn clean package"
      //     }
      //   }
      // }

      stage("Maven release") {
        when {
          expression{
            "${env.BRANCH_NAME}" ==~ /release\/.*/
          }
        }
        steps {
          echo "Maven release"

          withCredentials([sshUserPrivateKey(credentialsId: 'jenkins-git', keyFileVariable: 'JENKINS_GIT', usernameVariable: 'jaime-paredes')]) {
            sh 'mvn -B -Darguments="-Dmaven.test.skip=true -Dmaven.deploy.skip=true -DlocalCheckout=false" -DlocalCheckout=false -DtagNameFormat="V@{project.version}" -DgitRepositoryUrl=https://github.com/jaime-paredes/ms-iclab.git -Dresume=false release:prepare release:perform'
          }

          // git credentialsId: 'jenkins-git', url: 'git@github.com:jaime-paredes/ms-iclab.git', branch: "${env.BRANCH_NAME}"
          script {
            lastStage = env.STAGE_NAME
            // sh 'mvn -B -Darguments="-Dmaven.test.skip=true -Dmaven.deploy.skip=true" -DtagNameFormat="V@{project.version}" -DgitRepositoryUrl=git@github.com:jaime-paredes/ms-iclab.git -Dresume=false release:prepare release:perform'
          }
        }
      }

      stage("Get back to develop") {
        when {
          expression {
            "${env.BRANCH_NAME}" ==~ /release\/.*/
          }
        }
        steps {
          echo "Get back to develop"
          script {
            lastStage = env.STAGE_NAME
            sh "git fetch --all"
            // sh "git checkout develop"
          }
        }    
      }
    }   
}
