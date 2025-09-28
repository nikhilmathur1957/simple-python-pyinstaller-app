pipeline {
    agent any   // run on any available Jenkins agent

    environment {
        VENV = ".venv"   // virtual environment directory
    }

    stages {
        stage('Build') {
            steps {
                echo "Creating virtual environment and installing dependencies"
                sh '''
                    python3 --version
                    python3 -m venv ${VENV}
                    . ${VENV}/bin/activate
                    pip install --upgrade pip
                    pip install -r requirements.txt
                '''
            }
        }

        stage('Test') {
            steps {
                echo "Running unit tests with pytest"
                sh '''
                    . ${VENV}/bin/activate
                    pytest -q --disable-warnings --maxfail=1
                '''
            }
        }

        stage('Deploy') {
            when {
                branch 'main'   // only deploy when building the main branch
            }
            steps {
                echo "Deploying application to staging environment..."
                // Example deployment: copy files to staging server via SSH
                sshagent(['deploy-ssh']) {
                    sh '''
                        rsync -avz --exclude ".venv" ./ user@staging-server:/home/user/myapp
                        ssh user@staging-server "
                            cd /home/user/myapp &&
                            python3 -m venv .venv &&
                            . .venv/bin/activate &&
                            pip install -r requirements.txt &&
                            systemctl --user restart myapp
                        "
                    '''
                }
            }
        }
    }
}

    