#!groovy

pipeline {
  agent none
  stages {
    stage('Docker Build CentOS') {
      agent any
      steps {
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
    }
// EOL CENTOS
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
          sh 'docker push udienz/docker-ansible:ubuntu2204'
        }
      }
    }
// EOL Ubuntu Jammy
  }
// EOL STAGES
}
