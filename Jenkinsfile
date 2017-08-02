pipeline {
  agent none

  options {
    buildDiscarder(logRotator(numToKeepStr: '2', artifactNumToKeepStr: '1'))
  }

  stages {
    stage ('Unit Test') {
      agent {
        label 'apache'
      }
      steps {
        sh 'ant -f text.xml -v'
        junit 'reports/result.xml'
      }
    }

    stage ('build'){
      agent {
        label 'apache'
      }
      steps {
        sh 'ant -f build.xml -v'
      }
      post {
        success {
          archiveArtifacts artifacts: 'dist/*.jar', fingerprint: true
        }
      }
    }

    stage ('deploy') {
      agent {
        label 'apache'
      }
      steps {
        sh "cp dist/rectangle_${env.BUILD_NUMBER}.jar /var/www/html/rectangles/all"
      }
    }
    
    stage ("Running on Centos") {
      agent {
        label 'CentOS'
      }
      steps {
        sh "wget http://jenkinsserver.jlab.com/rectangles/all/rectangle_${env.BUILD_NUMBER}.jar"
        sh "java -jar rectangle_${env.BUILD_NUMBER}.jar 3 4"
      }
    }
    stage ("Test on Debian") {
      agent {
        docker 'openjdk:8u131-jre'
      }
      steps {
        sh "wget http://jenkinsserver.jlab.com/rectangles/all/rectangle_${env.BUILD_NUMBER}.jar"
        sh "java -jar rectangle_${env.BUILD_NUMBER}.jar 3 4"
      }
    }

    stage ("Promote to Green") {
      steps {
        sh "cp /var/www/html/all/rectangle_${env.BUILD_NUMBER}.jar /var/www/html/green/rectangle_${env.BUILD_NUMBER}.jar"
      }
    }
  } 
}
