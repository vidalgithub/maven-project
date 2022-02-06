pipeline {
    agent {
                label ("master")
            }
    parameters {
   string(name: 'BRANCH',
           defaultValue: 'develop',
           description: '''branch''')
    string(name: 'WARNTIME',
           defaultValue: '3',
           description: '''Warning time (in minutes) before starting upgrade''')
  }
    stages {
        stage('Valading code syntax') {
            steps {
                echo 'Hello Wgdfwgngfmmgjhmghjfwgwfgeorld'
                sh 'sleep 5'
            }
        }

        stage('testing code') {
            steps {
                echo 'Hello World'
                sh 'sleep 5'
            }
        }


         stage('Junit report') {
            steps {
                echo 'Hello World'
                sh 'sleep 5'
            }
        }




        stage('Sonarqube analysis') {
            steps {
                echo 'Hello World'
                sh 'sleep 5'
            }
        }

          

        stage('Sonarqube quality gate') {
            steps {
                echo 'Hello World'
                sh 'sleep 5'
            }
        }


        stage('warning') {
        steps {
            script {
                notifyUpgrade(currentBuild.currentResult, "WARNING")
                sleep(time:env.WARNTIME, unit:"MINUTES")
                }
            }
        }
        stage('Buiding docker images') {
            steps {
               sh '''
                docker build -t development:${BUILD_NUMBER}   .
                '''
            }
        }
    
    
         stage('Generating compose file ') {
            steps {
                sh'''
cat <<EOF > docker-compose.yml
  httpd:
       image: development:${BUILD_NUMBER}
       expose:
        - 99
        - 80
       container_name: httpd
       restart: always  
       ports:
        - 99:80
       environment:
         CITY: Kathleen
         COUNTRY: U.S.A
EOF

'''
            }
        }
    
         stage('Deploying to Development cluster') {
            steps {
                sh '''
                docker-compose up -d 
                sh '''
            }
        }
    
    }
    post {
    always {
      script {
        notifyUpgrade(currentBuild.currentResult, "POST")
      }
    }
    cleanup {
      deleteDir()
    }
  }
}   

def notifyUpgrade(String buildResult, String whereAt) {
  if (BRANCH == 'origin/develop') {
    channel = 'foucus-group'
  } else {
    channel = 'foucus-group'
  }
  if (buildResult == "SUCCESS") {
    switch(whereAt) {
      case 'WARNING':
        slackSend(channel: channel,
                color: "#439FE0",
                message: "DEVELOPMENT: Upgrade starting in ${env.WARNTIME} minutes @ ${env.BUILD_URL}")
        break
    case 'STARTING':
      slackSend(channel: channel,
                color: "good",
                message: "DEVELOPMENT: Starting upgrade @ ${env.BUILD_URL}")
      break
    default:
        slackSend(channel: channel,
                color: "good",
                message: "DEVELOPMENT: Upgrade completed successfully @ ${env.BUILD_URL}")
        break
    }
  } else {
    slackSend(channel: channel,
              color: "danger",
              message: "DEVELOPMENT: Upgrade was not successful. Please investigate it immediately.  @ ${env.BUILD_URL}")
    }
}


