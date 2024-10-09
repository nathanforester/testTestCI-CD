pipeline {
    agent any
    parameters {
        booleanParam(name: 'Refresh',
                    defaultValue: false,
                    description: 'Read Jenkinsfile and exit.')
		    }
    
      stages {

        stage('Unit Tests P1') {
            steps {
                sh '''
                      python3 -m pytest ./prime/tests/test_unit.py
                   '''
            }
        }

        stage('Unit Tests P2') {
            steps {
                sh '''
                      python3 -m pytest ./converter/tests/test_unit.py
                   '''
            }
        }
        
        stage('Integration Tests') {
            steps {
                sh '''
                      python3 -m pytest ./main/tests/test_unit.py
                   '''
            }
        }
        stage('docker prune') {
            steps {
                sh 'docker system prune -a -f'
            }
        }

        stage('docker compose') {
            steps {
                sh 'docker-compose build'
            }
        }

        stage('git credentials') {
            steps {
                sh '''
                   git config --global user.email "nfeugene86@gmail.com"
                   git config --global user.name "nathanforester"
                   git remote set-url origin git@github.com:nathanforester/testTestCI-CD.git
                   '''
            }
        }

        stage('merge feature to dev') {
            steps {
                sh '''
                   git checkout -f origin/dev
                   git merge origin/featureA
                   git push origin HEAD:dev
                   '''
            }
        }

        stage ('merge dev to main') {
            steps {
                sh '''
                   git checkout origin/main
                   git merge origin/dev
                   git push origin HEAD:main
                   '''
            }
        }

        stage('connect via ssh deploy server') {
            steps {
                sh '''
                   #!/bin/bash
                   ssh -i /home/jenkins/.ssh/myKey -o StrictHostKeyChecking=no ubuntu@35.177.245.108 << EOF
                   git clone https://github.com/nathanforester/testTestCI-CD.git
                   sudo docker-compose -f /home/ubuntu/testTestCI-CD/docker-compose.yaml down
                   sudo docker system prune -a -f
                   sudo docker-compose -f /home/ubuntu/testTestCI-CD/docker-compose.yaml up -d
                   sudo rm -R /home/ubuntu/testTestCI-CD
                   << EOF
                '''
            }
        }
        
    }
}// some text here
