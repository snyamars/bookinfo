#!/usr/bin/env groovy

node {
  
   
   stage('checkout') {
        checkout scm
    }

    stage('check java') {
        sh "java -version"
    }

    stage('clean') {
    //  sh "/usr/bin/mvn clean"
    }
  
   stage('packaging') {
        //sh "./mvnw package -Pprod -DskipTests"
      //  sh "/usr/bin/mvn package -DskipTests"
      //  archiveArtifacts artifacts: '**/target/*.jar', fingerprint: true
    }
  
  stage('s2i'){
    //sh "/var/jenkins_home/s2i build bookinfo/productpage snyamars007/springboot-jdk8"
  }
  
    /***/ 
    stage ('docker build'){
      withCredentials([[$class: "UsernamePasswordMultiBinding", usernameVariable: 'DOCKERHUB_USER', passwordVariable: 'DOCKERHUB_PASS', credentialsId: 'dockerhub_id']]) {
      sh 'docker login --username $DOCKERHUB_USER --password $DOCKERHUB_PASS'
    }
    
    def serverImage = docker.build("snyamars007/mongodb:${env.BUILD_ID}", '/var/jenkins_home/workspace/bookinfo/src/mongodb')
       serverImage.push()
    def serverImage1 = docker.build("snyamars007/mysql:${env.BUILD_ID}", '/var/jenkins_home/workspace/bookinfo/src/mysql')
    serverImage1.push()
    def serverImage2 = docker.build("snyamars007/details:${env.BUILD_ID}", '/var/jenkins_home/workspace/bookinfo/src/details')
    serverImage2.push()
    def serverImage3 = docker.build("snyamars007/productpage:${env.BUILD_ID}", '/var/jenkins_home/workspace/bookinfo/src/productpage')
    serverImage3.push()
    def serverImage4 = docker.build("snyamars007/ratings:${env.BUILD_ID}", '/var/jenkins_home/workspace/bookinfo/src/ratings')
    serverImage4.push()

    sh 'docker logout'
   }
   
  /***
      stage 'notifyKubernetes'
     try{
      sh "kubectl apply -f ./kube/bookinfo.yaml"
   
     }catch(e){
      println("no prior deployment exists")
     }
        
  ***/   
}//end of node
