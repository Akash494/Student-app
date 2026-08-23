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
                        credentialsId: 'dockerhub',
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
          when {
            expression {
              return params.buildDockerImage
            }
         }
          steps {
            dir('appdir/src') {
              sh '''
                docker build -t ${DOCKER_IMAGE}:${IMAGE_TAG} .
                docker push akash228/student-app:v1
                 '''
            }
         }
      }    
        
stage('Ansible Deployment') {
    steps {
        withCredentials([
            usernamePassword(
                credentialsId: 'jenkins-ansible',
                usernameVariable: 'SERVER1_USER',
                passwordVariable: 'SERVER1_PASS'
            ),
            usernamePassword(
                credentialsId: 'kubernetes-ansible',
                usernameVariable: 'SERVER2_USER',
                passwordVariable: 'SERVER2_PASS'
            )
        ]) {
            sh '''
                ansible-playbook \
                  -i ansible/inventory.yaml \
                  ansible/playbook.yaml \
                  -e "server1_user=$SERVER1_USER" \
                  -e "server1_pass=$SERVER1_PASS" \
                  -e "server2_user=$SERVER2_USER" \
                  -e "server2_pass=$SERVER2_PASS"
            '''
        }
    }
}
        
    }
}
