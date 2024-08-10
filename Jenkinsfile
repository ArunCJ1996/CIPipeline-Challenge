pipeline{
   agent {
    docker {
      image 'maven:3.6.3-jdk-11'
      args '-v /root/.m2:/root/.m2'
    }
  }
  stages {
      stage("Maven Build"){
          steps{
             script{
                stage=env.STAGE_NAME
               }
              sh 'mvn -B -DskipTests clean package'
             
          }
        
      }
      stage('Maven Test'){
            steps{
               script{
                  stage=env.STAGE_NAME
            }
                sh 'mvn test'
            }
            post{
            always{
                junit 'target/surefire-reports/*.xml'
            }
        }
        }
     stage('Collect artifact'){
       steps{
           archiveArtifacts artifacts: 'target/*.jar', followSymlinks: false
       }
     }
     stage('deploy to artifactory'){
        steps{
                 rtUpload (
                     serverId: 'Artifactory12',
                     spec: '''{
          "files": [
            {
              "pattern": "target/*.jar",
              "target": "sample-Repository"
            }
         ]
    }'''
                    )
           script{
                  stage=env.STAGE_NAME
            }
        }
     
     }
     
    }
    post {  
         always {  
             echo 'This will always run'  
         }  
         success {   
            echo "========Deploying executed successfully========"
            emailext attachLog: true, body: "<b>Example</b><br>Project: ${env.JOB_NAME}", from: 'aruncjayaprakash@gmail.com',compressLog: true, mimeType: 'text/html', replyTo: '', subject: "Deploy Successfull Project ${env.JOB_NAME}", to: "aruncjayaprakash@gmail.com";
         }  
         failure {  
             mail bcc: '', body: "<b>Example</b><br>Project: ${env.JOB_NAME} <br>Build Number: ${env.BUILD_NUMBER} <br> Stage Name: $stage <br> URL de build: ${env.BUILD_URL}", cc: 'kousigowthaman99@gmail.com', charset: 'UTF-8', from: 'aruncjayaprakash@gmail.com', mimeType: 'text/html', replyTo: '', subject: "Deployment failed for Project -> ${env.JOB_NAME}", to: "aruncjayaprakash@gmail.com";  
         }  
         unstable { 
             echo 'This will run only if the run was marked as unstable'  
         }  
         changed {  
             echo 'This will run only if the state of the Pipeline has changed'   
             echo 'For example, if the Pipeline was previously failing but is now successful'  
         }  
     }
  }
