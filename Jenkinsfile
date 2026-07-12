node {
    stage('Checkout') {
        git branch: 'main',
            url: 'https://github.com/scroll2learn/custom-jenkins-2.git'
    }

    stage('Install Dependencies') {
        sh '''
            echo "Creating virtual environment"
            python3 -m venv .venv

            echo "Upgrading pip"
            .venv/bin/python -m pip install --upgrade pip

            echo "Installing dependencies"
            .venv/bin/python -m pip install -r requirements.txt
        '''
    }

    stage('Run Tests') {
        sh '''
            echo "Running pytest"
            .venv/bin/python -m pytest -v
        '''
    }

    stage('Deploy Application') {
        sh '''
            echo "Stopping old application, if running"
            pkill -f "python.*app.py" || true

            echo "Starting Python application"
            nohup .venv/bin/python app.py > app.log 2>&1 &

            echo $! > app.pid
        '''
    }

    stage('Verify Deployment') {
        sh '''
            echo "Waiting for application to start"
            sleep 5

            echo "Application log:"
            cat app.log || true

            echo "Testing application"
            curl -f http://localhost:5000
        '''
    }
}
