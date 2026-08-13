pipeline {

    agent any
    
    environment {
        DOCKER_IMAGE = "akash228/student-app"
        IMAGE_TAG    = "v1"
    }
    
    stages {
        
         stage('Docker Login') {
            steps {
                echo 'Logging in to Docker Hub...'

                withCredentials([
                    usernamePassword(
                        credentialsId: 'docker-hub',
                        usernameVariable: 'DOCKER_USERNAME',
                        passwordVariable: 'DOCKER_PASSWORD'
                    )
                ]) {
                    sh '''
                        echo "$DOCKER_PASSWORD" | docker login \
                            -u "$DOCKER_USERNAME" \
                            --password-stdin
                    '''
                }
            }
        }
        
        stage('Docker Build') {
          steps {
            dir('Student-app/appdir/src') {
              sh '''
                docker build -t ${DOCKER_IMAGE}:${IMAGE_TAG} .
                docker push akash228/student-app:v1
                 '''
            }
         }
      }    

        
    }
}
