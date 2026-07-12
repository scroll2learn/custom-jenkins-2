node {
    stage('Checkout') {
        git branch: 'main',
        url: 'https://github.com/scroll2learn/custom-jenkins-2.git'
    }

    stage('Install Dependencies') {
        sh '''
            python3 -m venv .venv
            . venv/bin/activate
            pip install -r requirements.txt
        '''
    }

    stage('Run Tests') {
        sh '''
            . venv/bin/activate
            pytest -v
        '''
    }

    stage(' Docker') {
        sh '''
            pkill -f "python3 app.py" || true

            . venv/bin/activate

            nohup python3 app.py > app.log 2>&1 &
        '''
    }

    stage('Verify Deployment') {
        sh '''
            sleep 5
            curl -f http://localhost:5000
        '''
    }
}