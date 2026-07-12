node {
    stage('Checkout') {
        git branch: 'main',
            url: 'https://github.com/scroll2learn/custom-jenkins-2.git'
    }

    stage('Install Dependencies') {
        sh '''
            python3 -m venv .venv

            .venv/bin/python -m pip install --upgrade pip
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
            echo "Stopping old Flask application"

            if [ -f app.pid ]; then
                kill $(cat app.pid) 2>/dev/null || true
                rm -f app.pid
            fi

            pkill -f ".venv/bin/python.*app.py" || true

            echo "Starting Flask application"

            export JENKINS_NODE_COOKIE=dontKillMe

            nohup .venv/bin/python app.py > app.log 2>&1 &

            echo $! > app.pid

            sleep 5

            echo "PID:"
            cat app.pid

            echo "Process details:"
            ps -p $(cat app.pid) -f

            echo "Listening on port 5000:"
            ss -lntp | grep 5000

            echo "Application log:"
            cat app.log
        '''
    }

    stage('Verify Deployment') {
        sh '''
            echo "Testing Flask application"
            curl -f http://localhost:5000
        '''
    }
}
