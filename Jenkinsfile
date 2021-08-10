pipeline {
    agent any
   
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
        stage('Test') {
            steps {
                junit '**/target/surefire-reports/TEST-*.xml'
                archiveArtifacts 'target/*.jar'
            }
        }
       
        post{
            always{
                echo "Name of the committer ${env.COMMITTER}"
                echo "Email of the committer ${env.EMAIL}"
                }
            }
        }
    }
}
