pipeline {
    agent any

     environment{
       registryCredential = 'ecr:ap-northeast-1:ishanpandey2000'
       appRegistry = "475263483524.dkr.ecr.ap-northeast-1.amazonaws.com/capstoneproject"
       capstoneRegistry = "https:// 475263483524.dkr.ecr.ap-northeast-1.amazonaws.com"
       cluster = "CapstoneProject"
        service = "capstoneService"
   }

    stages {
        stage('Clone Website') {
            steps {
                git url:'https://github.com/ishanpandey70/website.git'
            }
        }

       stage("Docker Build Image"){
           steps{
             script{
                dockerImage = docker.build(appRegistry+ ":$BUILD_NUMBER",".")
             }
           }
      }

       stage('Test Website') {
            steps {
                // Run tests on the website
                sh 'echo "Running tests on the website"'
            }
       }

       stage("Upload App Image"){
         steps{
            script{
                docker.withRegistry(capstoneRegistry, registryCredential){
                    dockerImage.push("$BUILD_NUMBER")
                    dockerImage.push('latest')
                }
            }
         }
      }

      stage('Deploy to ecs'){
         when {
                branch "master"
         }
         steps{
            withAWS(credentials: 'ishanpandey2000', region: 'ap-northeast-1'){
                sh 'aws ecs update-service --cluster ${cluster} --service ${service} --force-new-deployment'
            }
         }
      }

    }
}