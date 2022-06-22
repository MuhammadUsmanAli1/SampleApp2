pipeline {
    agent any
    environment {
        AWS_ACCESS_KEY_ID     = credentials('jenkins-aws-secret-key-id')
        AWS_SECRET_ACCESS_KEY = credentials('jenkins-aws-secret-access-key')
        ARTIFACT_NAME = 'test.zip'
        AWS_S3_BUCKET = 'smshandler'
        AWS_EB_APP_NAME = 'smshandler'
        AWS_EB_ENVIRONMENT = 'Smshandler-env'
        AWS_EB_APP_VERSION = "${BUILD_ID}"
    }
     triggers {
        githubPush()
      }
    stages {
        stage('Restore packages'){
           steps{
               bat 'dotnet restore MySampleApp.sln'
            }
         }        
        stage('Clean'){
           steps{
               bat 'dotnet clean MySampleApp.sln --configuration Release'
            }
         }
        stage('Build'){
           steps{
               bat 'dotnet build MySampleApp.sln --configuration Release --no-restore'
            }
         }
        stage('Test: Unit Test'){
           steps {
                //bat 'dotnet test MySampleApp/ --configuration Release --no-restore'
               bat 'echo no test added'
             }
          }
        stage('Publish'){
             steps{
               bat 'dotnet publish MySampleApp/MySampleApp.vbproj --configuration Release --no-restore'
             }
        }
        
        stage('make zip'){
            steps {
                echo "working"
               script {
                    zip dir: 'MySampleApp/bin', glob: '', zipFile: 'test.zip'
                }
            }
        }
   
        stage('Copy to s3'){
        steps {
            bat 'echo "all good'
            archiveArtifacts 'test.zip'
            bat 'aws configure set region us-east-1'
            bat 'aws s3 cp test.zip s3://smshandler'
  }          
        }                     
                stage('Create and Deploy New Version toBeanstalk') {
            steps {
                bat 'echo "working'
                bat 'aws elasticbeanstalk create-application-version --application-name %AWS_EB_APP_NAME% --version-label %AWS_EB_APP_VERSION% --source-bundle S3Bucket=%AWS_S3_BUCKET%,S3Key=%ARTIFACT_NAME%'
bat 'aws elasticbeanstalk update-environment --application-name %AWS_EB_APP_NAME% --environment-name %AWS_EB_ENVIRONMENT% --version-label %AWS_EB_APP_VERSION%'          }
      
            }
        }        
        
}
