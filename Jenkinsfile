pipeline {
   agent any
   
   options {
      disableConcurrentBuilds()
   }
   
   tools{
      maven 'mvn-3.5.4'
   }
   
   stages {
      stage('Hello') {
         steps {
            echo 'Hello World'
            echo "build num: ${env.BUILD_NUMBER}  $env.JENKINS_URL"
            sh "mvn -version"
            sh "printenv"
         }
      }
      stage('PMD') {
         steps {
            configFileProvider([configFile(fileId: "maven-global-settings", variable: "MAVEN_GLOBAL_ENV")]) {
               sh "mvn -s $MAVEN_GLOBAL_ENV pmd:pmd"
            }
         }
      }
      stage('Junit') {
         steps {
            configFileProvider([configFile(fileId: "maven-global-settings", variable: "MAVEN_GLOBAL_ENV")]) {
               sh "mvn -s $MAVEN_GLOBAL_ENV test"
            }
         }
      }
   }
   
   post{
      failure{
         mail to:'1400902533@qq.com', subject: 'The pipeline failed', body: 'failed'
      }
      success{
         mail to:'1400902533@qq.com', subject: 'The pipeline success', body: 'success'
      }
      always{
         pmd(canRunOnFailed:true, pattern: '**/target/pmd.xml')
         junit allowEmptyResults: true, testResults: "**/target/surefire-reports/*.xml"
      }
   }
}
