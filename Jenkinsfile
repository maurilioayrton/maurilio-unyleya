pipeline {  
    environment {
        dockerImage = "${image}"
    }
    agent any 
    stages { 
        stage('Remove Container Antigo'){ 
            steps{
                script{
                    try{
                        sh 'docker rm -f mobead_image_build-dev'
                    } catch (Exception e) {
                        sh "echo $e"
                    }

                } 
                
            }
        }
        stage('Build image') {
            steps{
                script {
                    dockerImage = docker.build registry + ":$BUILD_NUMBER"
                }
            }
        }
        stage('Subindo container de desenvolvimento') {
            steps{
                script {
                  try {
                      sh 'docker run -d -p 83:80 --name=mobead_image_build-dev ' + dockerImage
                  }catch (Exception e){
                      slacksend (color: 'error', message: "[ FALHA ] Não foi possível subir container - ${BUILD_URL} em ${currentBuild.duration}s", tokenCredentialId: 'slackuser')
                      sh "echo $e"
                      currentBuild.result = 'ABORTED'
                      error ('Erro')
                }
            }
        }
        stage ('Notificando o Usuario'){
            steps {
                slacksend (color: 'good', message: '[ Sucesso ]o novo build esta disponível em: http://http://192.168.15.140:83/ ', tokenCredentialId: 'slackuser')
            }
        }
    }
}