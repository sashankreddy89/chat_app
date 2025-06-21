pipeline{
    agent any
    stages {
        stage('cloning repo from git hub'){
            steps {
                git branch: 'main', credentialsId: 'git-pat', url: 'https://github.com/sashankreddy89/chat_app.git'
            }
        }
        
        stage('copying the chatapp to node') {
            steps {
              sshagent(['chatapp']) {
                    sh '''scp -o StrictHostKeyChecking=no \\
                              -o ProxyCommand="ssh -o StrictHostKeyChecking=no ubuntu@35.180.21.186 -W %h:%p" \\
                              -r "\$WORKSPACE"/* ubuntu@10.0.3.8:~/chat_app/'''
                }
            }
        }
        
        stage('configuring the node') {
            steps {
              sshagent(['chatapp']) {
                    sh """
                    ssh -o StrictHostKeyChecking=no \\
                              -o ProxyCommand="ssh -o StrictHostKeyChecking=no ubuntu@35.180.21.186 -W %h:%p" \\
                              ubuntu@10.0.3.8 bash -c '
                              source venv/bin/activate &&
                              cd chat_app
                              pip install -r requirements.txt
                              cd fundoo
                              python3 manage.py makemigrations
                              python3 manage.py migrate
                              deactivate
                              sudo systemctl restart chatapp
                              '
                    """
                }
            }
        }
    }
    post {
    success {
      echo "Deployment completed successfully!"
    }
    failure {
      echo "Deployment failed. Check logs."
    }
  }
}
