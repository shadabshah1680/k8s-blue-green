pipeline {
    agent {
        label 'docker_labelnode_number'
    }
	stages {
		
		stage('Clone Repo') {
			steps {
					sh "git clone -b main https://github.com/shadabshah1680/k8s-blue-green.git"
				}
			}
		stage('Build Docker Image') {
			steps {
					sh "sudo docker build -t shadabshah1680/multi-server:blue_green_kubernetes  -f k8s-blue-green/Dockerfile ."
				}
			}
		
		stage('Upload Image To Dockerhub') {
			steps {
		            sh "name=`aws ssm get-parameter --name docker-username --query \'Parameter.Value\' --region us-east-1 --output text` && DOCKER_PASSWORD=`aws ssm get-parameter --name docker-password --query \'Parameter.Value\' --region us-east-1 --output text` && sudo docker login -u \${name} -p \${DOCKER_PASSWORD} && sudo docker push shadabshah1680/multi-server:blue_green_kubernetes"
				}
			}
		stage('Blue replication controller') {
			steps {
					sh 	"sudo kubectl apply -f ./blue-replication-controller.yaml"
				}
			}
		
		stage('Green replication controller') {
			steps {
					sh "sudo kubectl apply -f ./green-replication-controller.yaml"

			}
		}
		stage('Create the service in kubernetes cluster traffic to blue controller') {
			steps {
					sh 	"sudo kubectl apply -f ./blue-service.yaml"
			}
		}
		stage('User approve to continue') {
            steps {
                input "Ready to change redirect traffic to green?"
            }
        }
		stage('Create the service in kubernetes cluster traffic to green controller') {
			steps {
					sh	"sudo kubectl apply -f ./green-service.yaml"
			}
		}
		stage('Clean Ws') {
			steps {
					sh	"rm -rf /home/jenkins/workspace/k8s-bg-deployment/*"
			}
		}

	}
}