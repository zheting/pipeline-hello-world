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

      stage('JaCoCo') {
           steps {
              configFileProvider([configFile(fileId: "maven-global-settings", variable: "MAVEN_GLOBAL_ENV")]) {
                 sh "mvn -s $MAVEN_GLOBAL_ENV clean install"
              }

              jacoco(
                //代码覆盖率统计文件位置，Ant风格的路径表达式
                execPattern:'target/**/*.exec',
                //classes文件位置，Ant风格的路径表达式
                classPattern:'target/classes',
                //源码文件位置，Ant风格的路径表达式
                sourcePattern:'src/main/java',
                //排除分析的位置，Ant风格的路径表达式
                exclusionPattern:'src/test*',
                //是否禁用每行覆盖率的源文件显示
                skipCopyOfSrcFiles:false,
                /*
                    如果为true,则对各个维度的覆盖率进行比较。
                    如果任何一个维度的当前覆盖率小于最小覆盖率阈值，则构建状态为失败；
                    如果当前覆盖率在最大阈值和最小阈值之间，则当前构建状态为不稳定；
                    如果当前覆盖率大于最大阈值，则构建状态为成功。
                */
                changeBuildStatus: true,
                //字节码指令覆盖
                minimumInstructionCoverage:'30', maximumInstructionCoverage:'70',
                //行覆盖率
                minimumLineCoverage:'30', maximumLineCoverage:'70',
                //圈复杂度覆盖率
                minimumComplexityCoverage:'30', maximumComplexityCoverage:'70',
                //方法覆盖率
                minimumMethodCoverage:'30', maximumMethodCoverage:'70',
                //类覆盖率
                minimumClassCoverage:'30', maximumClassCoverage:'70',
                //分支盖率
                minimumBranchCoverage:'30', maximumBranchCoverage:'70',
                //如果为true，则只有所有维度的覆盖率变化量的绝对值小于相应的变化量阈值时，构建结果才为成功
                buildOverBuild:true,
                //以下是各个维度覆盖率的变化量阈值
                deltaInstructionCoverage:'80',
                deltaLineCoverage:'80',
                deltaMethodCoverage:'80',
                deltaClassCoverage:'80',
                deltaComplexityCoverage:'80',
                deltaBranchCoverage:'80'
              )
      }
	}
      stage('sonarQube') {
            steps {
                withSonarQubeEnv('sonarqube'){
                    sh "mvn clean package org.sonarsource.scanner.maven:sonar-maven-plugin:3.7.0.1746:sonar -Dsonar.host.url=${SONAR_HOST_URL} -Dsonar.login=${SONAR_AUTH_TOKEN}"
                }
            }
       }

      stage('Quality Gate') {
           steps {
              timeout(time:1, unit:'HOURS'){
                waitForQualityGate abortPipeline:true
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
