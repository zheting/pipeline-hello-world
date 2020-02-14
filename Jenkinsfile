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
            sh "mvn -version"
            sh "printenv"
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
      
   }
}
