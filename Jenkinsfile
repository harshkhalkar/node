pipeline {
    agent any
    tools{
        nodejs 'mynodejs'
    }
    stages {
        stage('Git init')
        {
            steps{
                echo 'Pulling files from git'
                checkout scmGit(branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/harshkhalkar/node.git']])
            }
        }
        stage('Build') {
            steps {
                echo 'Building NodeJS'
                sh 'npm install'
            }
        }
        stage('Test') {
            steps {
                echo 'Testing Build'
                sh './node_modules/mocha/bin/_mocha --exit ./test/test.js'
            }
        }
        stage('Deploy') {
            steps {
                echo 'Deploying NodeJS project on live server'
                sshagent(['b1e89f3b-683b-42ff-a2ba-b1046f4cc7ce']){
                sh '''
                    ssh -o StrictHostKeyChecking=no ec2-user@<livesrver-public-ip> << 'ENDSSH'
                    sudo dnf update -y
                    sudo yum install nodejs npm git -y
                    mkdir /home/ec2-user/node
                    cd /home/ec2-user/node
                    sudo git init
                    sudo git pull https://github.com/harshkhalkar/node.git
                    sudo npm install
                    sudo npm install -g pm2
                    pm2 stop all && pm2 delete all
                    pm2 start index.js
                '''
                }
            }
        }
    }
}
