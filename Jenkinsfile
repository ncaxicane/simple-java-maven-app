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
                  sh 'git submodule foreach --recursive git reset --hard'  
                
                 sh '''
                    echo "I deleted everything"
                    ls -lah
                '''
            }
        }
    }
}
