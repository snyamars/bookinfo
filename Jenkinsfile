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
  
    /***/ 
    stage ('docker build'){
      withCredentials([[$class: "UsernamePasswordMultiBinding", usernameVariable: 'DOCKERHUB_USER', passwordVariable: 'DOCKERHUB_PASS', credentialsId: 'dockerhub_id']]) {
      sh 'docker login --username $DOCKERHUB_USER --password $DOCKERHUB_PASS'
    }
    def serverImage = docker.build('snyamars007/mongodb:${env.BUILD_ID}', './src/mongodb/Dockerfile')
    serverImage.push()
    def serverImage = docker.build('snyamars007/mysql:${env.BUILD_ID}', './src/mysql/Dockerfile')
    serverImage.push()
    def serverImage = docker.build('snyamars007/details:${env.BUILD_ID}', './src/details/Dockerfile')
    serverImage.push()
    def serverImage = docker.build('snyamars007/productpage:${env.BUILD_ID}', './src/productpage/Dockerfile')
    serverImage.push()
    def serverImage = docker.build('snyamars007/ratings:${env.BUILD_ID}', './src/ratings/Dockerfile')
    serverImage.push()

    sh 'docker logout'
   }
   
  /***/
      stage 'notifyKubernetes'
     try{
      sh "kubectl delete deployment coe-spring-webpromote"
     }catch(e){
      println("no prior deployment exists")
     }
     try{
          sh "kubectl delete svc coe-spring-webpromote"   
     }catch(e){
      println("no prior service exists")
     }
   
      sh "sleep 3s"
      sh "kubectl run --image=snyamars007/coe-spring-webpromote:latest coe-spring-webpromote  --port=8091"
      
      sh "kubectl expose deployment coe-spring-webpromote"
     / **/
    
      /***/   
}//end of node
