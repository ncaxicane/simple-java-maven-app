pipeline {
    agent any
    stages {
        stage('Build') {
            steps {
                sh 'echo "Hello World"'
                sh '''
                    echo "Multiline shell steps works too"
                    ls -lah
                '''
                
           sh 'git reset --hard'
    sh 'git clean -f -d'
    sh 'git submodule foreach --recursive git reset --hard'
    sh 'git submodule update -f --init --recursive'
    sh 'git fetch --tags -f'
             
                
                 sh '''
                    echo "I deleted everything"
                    ls -lah
                '''
                
            }
        }
    }
}
