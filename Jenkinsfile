pipeline{
  agent{ 
    node{
      label 'dev'
        }
  }
  tools {         
    maven 'mymaven'         
    jdk 'jdk17'
    }
    environment {
        SCANNER_HOME=tool 'mysonar'
    }
    stages {         
      stage('CleanWS') {
            steps {                
              cleanWs()
            }
      }
      stage("Code") {
            steps {
                git "https://github.com/Durgaprasaddablikar/dockerwebapp.git"
            }
      }
      stage ("CQA"){             
        steps {
          withSonarQubeEnv('mysonar') {                     
              sh "mvn clean verify sonar:sonar -Dsonar.projectKey=MyProject"
          }
        }
      }
      stage("QualityGates"){
            steps {                 
              script {
                    waitForQualityGate abortPipeline: false, credentialsId: 'sonar'
                }
            }
      }
        
      stage ("build"){             
        steps {                 
          sh 'mvn clean package'                 
          sh 'cp -r target Docker-app'
        }
      }
      stage ("Artifact") {            
        steps {
                nexusArtifactUploader artifacts: [[artifactId: 'vprofile', classifier: '', file: 
                'target/vprofile-v2.war', type: 'war']], credentialsId: 'newnexus', groupId: 'com.visualpathit', nexusUrl: '44.192.114.46:8081', nexusVersion: 'nexus3', protocol: 'http', repository: 'myrepo', version: 'v2'
            }
      }
      stage ("DockerBuild"){
            steps {
                sh 'docker build -t prasaddablikar16/prasad:appimage Docker-app'                 
                sh 'docker build -t prasaddablikar16/prasad:dbimage Docker-db'
            }
      }
      stage ("trivy") {             
        steps {
                sh 'trivy image prasaddablikar16/prasad:appimage'                 
                sh 'trivy image prasaddablikar16/prasad:dbimage'
        }
      }
      stage ("Push"){  
        steps {                 
          script {
              withDockerRegistry(credentialsId: 'dockerhub') {                         
                  sh 'docker push prasaddablikar16/prasad:appimage'                         
                  sh 'docker push prasaddablikar16/prasad:dbimage'
              }
          }
        }
      }
      stage ("Stack") {           
        steps {
                sh 'docker stack deploy myapp --compose-file=compose.yml'             }
        }
     }
}
