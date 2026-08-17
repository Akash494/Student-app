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

stage('Test SSH') {
    steps {
        sshagent(['ansadm-credentials']) {
            sh '''
                ssh -o StrictHostKeyChecking=no \
                    ansadm@44.200.52.249 \
                    "hostname && whoami"
            '''
        }
    }
}

stage('Test Ansible Connectivity') {
    steps {
        ansibleAdhoc(
            inventory: 'ansible/inventory.yaml',
            credentialsId: 'ansadm-credentials',
            module: 'ping',
            hosts: 'all'
        )
    }
}
        
stage('Ansible Deployment') {
    steps {
        sh '''
            cat > ansible/inventory.yaml <<EOF
            [web]
            44.200.52.249 ansible_user=ansadm
            EOF

            echo "Inventory:"
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
