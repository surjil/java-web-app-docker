node{
    def buildNumber = BUILD_NUMBER
    stage("Git CheckOut"){
        git url: 'https://github.com/surjil/java-web-app-docker.git',branch: 'master'
    }
    
    stage(" Maven Clean Package"){
      def mavenHome =  tool name: "Maven", type: "maven"
      def mavenCMD = "${mavenHome}/bin/mvn"
      sh "${mavenCMD} clean package"
    } 
    
    stage("Build Dokcer Image") {
         sh "docker build -t surjil1612/java-web-app:${buildNumber} ."
    }
    
    stage("Docker Push"){
        withCredentials([string(credentialsId: 'Docker_Hub_Pwd', variable: 'Docker_Hub_Pwd')]) {
          sh "docker login -u surjil1612 -p ${Docker_Hub_Pwd}"
        }
        sh "docker push surjil1612/java-web-app:${buildNumber}"
        
    }

    stage("Deploy to dockercontainer in docker deployer"){
        sshagent(['docker_ssh_password']) {
            sh 'ssh -o StrictHostKeyChecking=no ubuntu@172.31.86.155 docker service rm -f cloudcandy || true'
            sh "ssh -o StrictHostKeyChecking=no ubuntu@172.31.86.155 docker run -d -p 8080:8080 --name cloudcandy surjil1612/java-web-app:${buildNumber}"
        }
    }
}    
