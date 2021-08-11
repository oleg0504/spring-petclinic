pipeline {
    agent any
    
    environment {
        CREDENTIALS_ID ='GCP-PetClinic'
        BUCKET = 'petclinic-bucket'
        PATTERN = '/var/lib/jenkins/workspace/test2/target/spring-petclinic-2.4.5.jar'
    }
   
    tools {
          maven "maven"
    }
    stages {
        stage('Build') {
            steps {
                checkout([$class: 'GitSCM', branches: [[name: '*/main']],
                 doGenerateSubmoduleConfigurations: false, 
                 extensions: [], 
                 submoduleCfg: [], 
                 userRemoteConfigs: [[url: 'https://github.com/oleg0504/spring-petclinic.git']]])
                script {
                env.COMMITTER = sh(script:'git log -n 1 --pretty=format:"%an"', returnStdout: true).trim()
                env.EMAIL = sh(script:'git log -n 1 --pretty=format:"%ae"', returnStdout: true).trim()
                }
                sh "mvn -Dmaven.test.failure.ignore=true clean package"
            }
        }   
        stage('Store to GCS') {
            steps{
                step([$class: 'ClassicUploadStep', credentialsId: env
                        .CREDENTIALS_ID,  bucket: "gs://${env.BUCKET}",
                      pattern: env.PATTERN])
                }
            }
        
        stage('Test') {
            steps {
                junit '**/target/surefire-reports/TEST-*.xml'
                archiveArtifacts 'target/*.jar'
            }
        }
          
    }
}
