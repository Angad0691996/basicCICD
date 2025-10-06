pipeline {
    agent any

    environment {
        EC2_CREDENTIALS = credentials('jenkins-ubuntu-key')
    }

    stages {
        stage('Clone Repository') {
            steps {
                git branch: 'main',
                    credentialsId: 'git-token',
                    url: 'https://github.com/Angad0691996/basicCICD.git'
            }
        }

        stage('Install Dependencies (Jenkins Host)') {
            steps {
                sh '''
                    sudo apt update && sudo apt install -y python3-venv
                    python3 -m venv venv
                    . venv/bin/activate && pip install --upgrade pip && pip install -r requirements.txt
                '''
            }
        }

        stage('Deploy to EC2') {
            steps {
                sshagent(credentials: ['jenkins-ubuntu-key']) {
                    sh '''
                    ssh -o StrictHostKeyChecking=no ubuntu@15.206.173.211 << 'EOF'
                        echo "🔹 Cleaning old processes..."
                        sudo pkill -f gunicorn || true
                        sudo pkill -f app.py || true

                        echo "🔹 Deploying new code..."
                        cd /home/ubuntu
                        sudo rm -rf basicCICD
                        git clone https://github.com/Angad0691996/basicCICD.git
                        cd basicCICD

                        echo "🔹 Setting up Python environment..."
                        sudo apt update && sudo apt install -y python3-venv
                        python3 -m venv venv
                        . venv/bin/activate
                        pip install --upgrade pip
                        pip install -r requirements.txt

                        echo "🔹 Starting Flask app..."
                        nohup python3 app.py > log.txt 2>&1 &
                        sleep 3
                        echo "✅ Application deployed and running!"
                    EOF
                    '''
                }
            }
        }
    }
}
