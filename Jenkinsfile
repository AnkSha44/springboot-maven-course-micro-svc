pipeline{
    agent any
    tools{
        maven 'maven'
    }
    stages{
        stage('checkout the code'){
            steps{
                git url:'https://github.com/AnkSha44/springboot-maven-course-micro-svc.git', branch: 'master'
            }
        }
        stage('build the code'){
            steps{
                sh 'mvn clean package'
            }
        }
     stage("sonar quality check"){
            steps{
                script{
                    withSonarQubeEnv(installationName: 'sonarscan', credentialsId: 'sonartoken') {
                            sh 'mvn sonar:sonar '
                    }

                    timeout(time: 1, unit: 'HOURS') {
                      def qg = waitForQualityGate()
                      if (qg.status != 'OK') {
                           error "Pipeline aborted due to quality gate failure: ${qg.status}"
                      }
                    }
                }
            }
        }
     stage('Docker Build') {
             steps {
                 sh 'docker build -t ankitsharma44/spring-petclinic:latest .'
      }
      }
     stage('Docker Push') {
        steps {
          withCredentials([usernamePassword(credentialsId: 'dockeraccess', passwordVariable: 'dockerHubPassword', usernameVariable: 'dockerHubUser')]) {
          sh "docker login -u ${env.dockerHubUser} -p ${env.dockerHubPassword}"
          sh 'docker push ankitsharma44/spring-petclinic:latest'
        }
      }
    }
}
}
