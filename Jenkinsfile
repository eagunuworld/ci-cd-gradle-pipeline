pipeline{
    agent any
    environment{
        VERSION = "${env.BUILD_ID}"
    }
    stages{
        stage("docker build & docker push"){
            steps{
                script{
                    withCredentials([string(credentialsId: 'docker_pass', variable: 'docker_password')]) {
                             sh '''
                                docker build -t 34.125.140.185:8085/springapp:${VERSION} .
                                docker login -u admin -p $docker_password 34.125.140.185:8085
                                docker push  34.125.140.185:8085/springapp:${VERSION}
                                docker rmi 34.125.140.185:8085/springapp:${VERSION}
                            '''
                       }
                   }
                }
             }
            stage("pushing the helm charts to nexus"){
                steps{
                    script{
                        withCredentials([string(credentialsId: 'docker_pass', variable: 'docker_password')]) {
                              dir('kubernetes/') {
                                 sh '''
                                     helmversion=$( helm show chart myapp | grep version | cut -d: -f 2 | tr -d ' ')
                                     tar -czvf  myapp-${helmversion}.tgz myapp/
                                     curl -u admin:$docker_password http://34.125.140.185:8081/repository/helm-hosted/ --upload-file myapp-${helmversion}.tgz -v
                                '''
                              }
                        }
                    }
                }
            }

      }
      
}
