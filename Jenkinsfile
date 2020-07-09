pipeline {
  agent none
  stages {
    stage('AppBuild') {
      agent {
        docker {
          image 'maven:3-alpine'
          args '-v /root/.m2:/root/.m2'
        }
      }
      steps {
        sh 'mvn clean install'
      }
    }
    stage ('image build and push') {
			agent any
				options {
					skipDefaultCheckout(true)
				}
			steps {
				script {
					docker.withRegistry('', 'dockerhub'){
					def app = docker.build("nancyrheniusbenny/gamutkart:${BUILD_NUMBER}")
					app.push()
					}
				}
			}	
		}
    
    stage('DeployApp') {
      agent any
      options {
        skipDefaultCheckout(true)
      }
      steps {
       //   withCredentials([sshUserPrivateKey(credentialsId: 'remotehost', keyFileVariable: 'REMOTEHOST_KEY', passphraseVariable: '', usernameVariable: 'vagrant')])
        //  script {
        //  def remote = [:]
        //  remote.name = "RemoteHost"
         // remote.host = "192.168.0.104"
       //   remote.allowAnyHosts = true
       //   remote.user = vagrant
       //   remote.identityFile = REMOTEHOST_KEY
       //   sshCommand remote: remote, command: "sudo docker ps -a" 
            sshagent(['nancy']) {
      //        sh "ssh -o StrictHostKeyChecking=no -i vagrant@192.168.0.105 hostname"
              sh 'ssh -o StrictHostKeyChecking=no vagrant@192.168.0.105 hostname'
              sh 'ssh -o StrictHostKeyChecking=no vagrant@192.168.0.105 "sudo docker rm -f gamutkart"' 
              sh 'ssh -o StrictHostKeyChecking=no vagrant@192.168.0.105 "sudo docker run -t -d -p 8090:8080 --name gamutkart nancyrheniusbenny/gamutkart:${BUILD_NUMBER}"'            
             // sh 'scp -o StrictHostKeyChecking=no target/gamutgurus.war nancy@192.168.0.105:/opt/tomcat/webapps'
            }
        //  }
      } 
    }
  }
  triggers {
    pollSCM('H/1 * * * * ')
  }
}
