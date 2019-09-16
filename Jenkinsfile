pipeline{
    agent any
    
    tools {maven 'mymaven'}
    
    
    stages{
        
       stage ('Build and Test') {
            steps{      
                  sh "mvn clean install"
             }
         }
        
       stage('Sonar') 
           { environment {
             scannerHome=tool 'sonar scanner'
           }
              steps {
                withSonarQubeEnv('Sonar') {
                sh "mvn sonar:sonar -Dsonar.host.url=http://18.224.155.110:9000"
                }
            }
          }
        stage ('Nexus'){
            steps{
             withCredentials([usernamePassword(credentialsId: 'sudipa_nexus', passwordVariable: 'pwd_2', usernameVariable: 'usr')]) {
             sh label: '', script: 'curl -u $usr:$pwd_2 --upload-file target/myWebApp_Test-0.0.1-SNAPSHOT.war http://18.224.155.110:8081/nexus/content/repositories/devopstraining/Akash_Spring/myWebApp_Test-0.0.1-SNAPSHOT.war'
           }   
         }
      }
         stage ('Deploy'){
            steps{
                 withCredentials([usernamePassword(credentialsId: 'akash-tom', passwordVariable: 'pass', usernameVariable: 'usr')]) {
                    sh label: '', script:'curl -u $usr:$pass ec2-13-233-157-196.ap-south-1.compute.amazonaws.com:8080/manager/text/undeploy?path=/Subha_Spring_Test_1'
                    sh label: '', script: 'curl -u  $usr:$pass --upload-file target/myWebApp_Test-0.0.1-SNAPSHOT.war http://ec2-13-233-157-196.ap-south-1.compute.amazonaws.com:8080/manager/text/deploy?config=file:/var/lib/tomcat8/myWebApp_Test-0.0.1-SNAPSHOT.war\\&path=/Subha_Spring_Test_1' 
               }
            }
        }
    }
     post {
   success {
      slackSend (color: '#00FF00', message: "SUCCESSFUL: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]' (${env.BUILD_URL})")
    }
    failure {
      slackSend (color: '#FF0000', message: "FAILED: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]' (${env.BUILD_URL})")
    }
    
  }
}


