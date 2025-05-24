pipeline {
	agent any

	environment {

		APP_NAME = 'spring-petclinic'
		AWS_USER = 'ec2-user'
		EC2_IP = '3.83.121.46'
		PEM_KEY = 'ec2-ssh-key'
	}

	stages {

		stage('Build') {
			steps {
				sh 'chmod +x mvnw'
				sh './mvnw clean package -DskipTests'
		}
	}

	stage('Docker Build & Push') {
		steps {
			script {
				dockerImage = docker.build("${APP_NAME}:${BUILD_NUMBER}")
				withDockerRegistry([credentialsId: 'dockerhub-creds', url: '']) {
            dockerImage.push("${BUILD_NUMBER}")
            dockerImage.push("latest")
          }
	 }
	}
}
	stage('Deploy to EC2') {
		steps {
			sh """
				scp -i ~/.ssh/${PEM_KEY} docker-compose.yml ${AWS_USER}@${EC2_IP}:/home/${AWS_USER}/
          ssh -i ~/.ssh/${PEM_KEY} ${AWS_USER}@${EC2_IP} '
            docker pull your-dockerhub-username/${APP_NAME}:latest &&
            docker stop ${APP_NAME} || true &&
            docker rm ${APP_NAME} || true &&
            docker run -d --name ${APP_NAME} -p 8080:8080 your-dockerhub-username/${APP_NAME}:latest
          '
        """
      }
    }
	}
}