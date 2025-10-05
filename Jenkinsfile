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

        stage('Install Dependencies') {
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
                    ssh -o StrictHostKeyChecking=no ubuntu@52.66.207.54 "
                        sudo pkill -f app.py || true;
                        cd /home/ubuntu &&
                        sudo rm -rf basicCICD &&
                        git clone https://github.com/Angad0691996/basicCICD.git &&
                        cd basicCICD &&
                        sudo apt update && sudo apt install -y python3-venv &&
                        python3 -m venv venv &&
                        . venv/bin/activate &&
                        pip install --upgrade pip && pip install -r requirements.txt &&
                        nohup python3 app.py > log.txt 2>&1 &
                    "
                    '''
                }
            }
        }
    }
}

