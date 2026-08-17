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
        sh '''
           PUBLIC_IP=$(curl -s http://checkip.amazonaws.com/)
           tee ansible/inventory.yaml > /dev/null <<EOF
           [localhost]
           $PUBLIC_IP ansible_user=ansadm
           EOF
           cat ansible/inventory.yaml
          '''
        ansiblePlaybook(
            playbook: 'ansible/playbook.yaml',
            inventory: 'ansible/inventory.yaml',
            credentialsId: 'ansadm-credentials'
        )
    }
}
        
    }
}
