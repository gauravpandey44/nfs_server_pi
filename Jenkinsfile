pipeline {
  
    agent {
      label 'PI' 
    }  
  
     environment {
            REPO = sh(script: 'echo "${GIT_URL}" | sed -e "s/https:\\/\\/github.com\\/.*\\///" | sed "s/.git//"',returnStdout: true).trim()
     }
  
    stages {

           stage('Build') {
               steps {

                    sh(""" 
                    mkdir -p /home/${USER}/${NODE_NAME}/dockers/${REPO}
                    cp -p ${WORKSPACE}/* /home/${USER}/${NODE_NAME}/dockers/${REPO}/
                    """)

                  }
            }
          stage('TEST') {
               steps {

                    sh(""" 
                    echo "TESTING"
                    """)

                  }
            }
          stage('Deploy') {
            steps {

                  sh("""
                  cd /home/${USER}/${NODE_NAME}/dockers/${REPO}/
                  docker-compose up -d
                  """)
            }

          }

     }
     post {
       always {
         echo "Sending Email"
         emailext subject: '$DEFAULT_SUBJECT',
                    body:  ''' 
                        $DEFAULT_CONTENT
                      ''',
                    recipientProviders: [
                        [$class: 'RequesterRecipientProvider']
                    ], 
                    replyTo: '$DEFAULT_REPLYTO',
                    to: '$DEFAULT_RECIPIENTS',
                    mimeType: 'text/html'
       }
    
    
  }
}
