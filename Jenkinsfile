#!groovy

pipeline {
  agent none
  options {
    timestamps()
    ansiColor("xterm")
    buildDiscarder(logRotator(numToKeepStr: "100"))
    timeout(time: 1, unit: "HOURS")
  }
  stages {
    stage('Docker Build CentOS') {
      agent any
      steps {
        bitbucketStatusNotify(buildState: 'INPROGRESS')
        sh 'docker build -t udienz/docker-ansible:centos7 centos7/'
      }
    }
    stage('Docker Push CentOS 7') {
      agent any
      steps {
        withCredentials([usernamePassword(credentialsId: 'dockerHub', passwordVariable: 'dockerHubPassword', usernameVariable: 'dockerHubUser')]) {
          sh "docker login -u ${env.dockerHubUser} -p ${env.dockerHubPassword}"
          sh 'docker push udienz/docker-ansible:centos7'
        }
      }
    } // EOL CENTOS
    stage('Docker Build Ubuntu Jammy') {
      agent any
      steps {
        sh 'docker build -t udienz/docker-ansible:jammy ubuntu/jammy'
      }
    }
    stage('Docker Push Ubuntu Jammy') {
      agent any
      steps {
        withCredentials([usernamePassword(credentialsId: 'dockerHub', passwordVariable: 'dockerHubPassword', usernameVariable: 'dockerHubUser')]) {
          sh "docker login -u ${env.dockerHubUser} -p ${env.dockerHubPassword}"
          sh 'docker push udienz/docker-ansible:jammy'
        }
      }
    } // EOL Ubuntu Jammy
  } // EOL STAGES
  post {
	always {
          deleteDir()
	      }
	failure {
            bitbucketStatusNotify(buildState: 'FAILED', commitId: "${env.GIT_COMMIT}")
        }
    unstable {
            bitbucketStatusNotify(buildState: 'SUCCESSFUL', commitId: "${env.GIT_COMMIT}")
        }
    success {
            bitbucketStatusNotify(buildState: 'SUCCESSFUL', commitId: "${env.GIT_COMMIT}")
        }
    aborted {
            bitbucketStatusNotify(buildState: 'FAILED', commitId: "${env.GIT_COMMIT}")
        }
    fixed {
            bitbucketStatusNotify(buildState: 'SUCCESSFUL', commitId: "${env.GIT_COMMIT}")
        }
  } // EOL post
}
