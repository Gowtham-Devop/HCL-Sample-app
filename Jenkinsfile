pipeline {
    agent any 
    tools { 
        maven 'maven' 
      
    }
stages { 
     
 stage('Preparation') { 
     steps {
      git 'https://github.com/Gowtham-Devop/HCL-Sample-app.git'   
     }
   }

   stage('Build') {
       steps {
       // Run the maven build

      //if (isUnix()) {
         sh 'mvn -Dmaven.test.failure.ignore=true install'
      //} 
      //else {
      //   bat(/"${mvnHome}\bin\mvn" -Dmaven.test.failure.ignore clean package/)
       }
//}
   }
 
  stage('Unit Test Results') {
      steps {
      junit '**/target/surefire-reports/TEST-*.xml'
      
     }
 }
 stage('Sonarqube') {
    environment {
        def scannerHome = tool 'sonarqube';
    }
    steps {
      withSonarQubeEnv('sonarqube') {
            sh "${scannerHome}/bin/sonar-scanner"
        }
        timeout(time: 10, unit: 'MINUTES') {
          waitForQualityGate abortPipeline: true
        }
    }
}
     stage('Artifact upload') {
      steps {
       nexusPublisher nexusInstanceId: '1234', nexusRepositoryId: 'releases', packages: [[$class: 'MavenPackage', mavenAssetList: [[classifier: '', extension: '', filePath: 'gameoflife-web/target/gameoflife.war']], mavenCoordinate: [artifactId: 'mygameoflife', groupId: 'com.wakaleo.gameoflife', packaging: 'war', version: '$BUILD_NUMBER']]]
      }
     }
    stage('Deploy War') {
      steps {
        sh label: '', script: 'ansible-playbook deploy.yml'
      }
 }
}      
}
