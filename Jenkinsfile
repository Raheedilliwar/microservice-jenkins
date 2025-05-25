/* groovylint-disable LineLength */
pipeline {
    agent any

    environment {
        EC2_USER = 'ec2-user'  // or 'ubuntu' based on your AMI
        EC2_HOST = '35.172.129.157'
    }

    stages {
        stage('Clone Repository') {
            steps {
                git url: 'https://github.com/Raheedilliwar/microservice-jenkins.git', branch: 'main'
            }
        }

        stage('Build Application') {
            steps {
                sh 'mvn clean package' // Change based on your tech stack
            }
        }

        stage('Deploy to EC2') {
            steps {
                sshagent(['ec2-ssh-key']) {
                    sh """
                    scp -o StrictHostKeyChecking=no target/app.jar ${EC2_USER}@${EC2_HOST}:/home/${EC2_USER}/app.jar
                    ssh -o StrictHostKeyChecking=no ${EC2_USER}@${EC2_HOST} 'nohup java -jar /home/${EC2_USER}/app.jar > app.log 2>&1 &'
                    """
                }
            }
        }
    }
}
