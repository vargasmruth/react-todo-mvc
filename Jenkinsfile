pipeline {
    agent any
    stages {        
        stage('Build') {
            agent {
                docker { 
                    image 'node:6' 
                    args '-u root:root'
                }
            }
            steps {
                sh "npm install"
                sh "echo 'Completed Build'"
            }
        }
        
        stage('Package') {
            agent any
            steps {
                sh "rm -rf *.tar"
                sh "docker build -t ruth:react_todo_mvc ."
                sh "docker save -o react_todo_mvc.tar ruth:react_todo_mvc"
                sh "echo 'Completed Package'"
            }
        }

        stage('Install') {
            agent any
            steps {
                sshagent (credentials: ['studen']) {
                    sh "scp -o StrictHostKeyChecking=no react_todo_mvc.tar studen@45.33.50.232:~/."
                    sh """
                        ssh -o StrictHostKeyChecking=no studen@45.33.50.232
                        docker rm  -f react_todo_mvc || true
                    """
                    sh """
                        ssh -o StrictHostKeyChecking=no studen@45.33.50.232
                        docker import react_todo_mvc.tar
                    """   
                    sh """
                        ssh -o StrictHostKeyChecking=no studen@45.33.50.232
                        docker run -d -p 10900:3000 --name react_todo_mvc ruth:react_todo_mvc
                    """                       
                    sh "echo 'Completed Install'"    
                }
            }
        }        
    }
}