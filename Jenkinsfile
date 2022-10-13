node("master") {
    stage('SCM') {
        git branch: 'bluegreen', url: 'git@github.com:shadabshah1680/k8s-blue-green.git'
		
    }
	stage('PreParation'){
		def status = sh(script:"ssh ubuntu@172.31.91.46 \"kubectl describe svc bluegreenloadbalancer  | grep Selector | cut -d\"=\" -f2\"", returnStdout: true).trim() 
			try {
				if ( status !=  'green' )
				{
		 		  sh "sed -i \"s|colour|green|g\" index.html && commit_id=`git rev-parse --short HEAD` && sudo docker build -t shadabshah1680/multi-server:\${commit_id}  -f Dockerfile . && sed -i \"s|latest|\${commit_id}|\" green-replication-controller.yaml && sed -i \"s|latest|\${commit_id}|\" green-replication-controller.yaml"
				  sh "commit_id=`git rev-parse --short HEAD` && name=`aws ssm get-parameter --name docker-username --query \'Parameter.Value\' --region us-east-1 --output text` && DOCKER_PASSWORD=`aws ssm get-parameter --name docker-password --query \'Parameter.Value\' --region us-east-1 --output text` && sudo docker login -u \${name} -p \${DOCKER_PASSWORD} && sudo docker push shadabshah1680/multi-server:\${commit_id}"
				  sh "sed -i \"s|colour|green|g\" qa-service.yaml && scp qa-service.yaml ubuntu@172.31.91.46:/tmp && ssh ubuntu@172.31.91.46 \"sudo kubectl apply -f /tmp/qa-service.yaml\""
				  input "Ready to change redirect traffic to green?"
				  sh 	"scp green-replication-controller.yaml ubuntu@172.31.91.46:/tmp && ssh ubuntu@172.31.91.46 \"sudo kubectl apply -f /tmp/green-replication-controller.yaml\""
				  sh 	"scp green-service.yaml ubuntu@172.31.91.46:/tmp && ssh ubuntu@172.31.91.46 \"sudo kubectl apply -f /tmp/green-service.yaml\""
				}
			catch( Exception ea) 
			{ 
				echo 'pass'
			} 
		}
    stage('BUILD') {
			def status = sh(script:"ssh ubuntu@172.31.91.46 \"kubectl describe svc bluegreenloadbalancer  | grep Selector | cut -d\"=\" -f2\"", returnStdout: true).trim() 
			try {
				if (status ==  'green' )
				{	sh "ls && pwd"
					sh "sed -i \"s|colour|blue|g\" index.html && commit_id=`git rev-parse --short HEAD` && sudo docker build -t shadabshah1680/multi-server:\${commit_id}  -f Dockerfile . && sed -i \"s|latest|\${commit_id}|\" blue-replication-controller.yaml && sed -i \"s|latest|\${commit_id}|\" green-replication-controller.yaml"
					sh "commit_id=`git rev-parse --short HEAD` && name=`aws ssm get-parameter --name docker-username --query \'Parameter.Value\' --region us-east-1 --output text` && DOCKER_PASSWORD=`aws ssm get-parameter --name docker-password --query \'Parameter.Value\' --region us-east-1 --output text` && sudo docker login -u \${name} -p \${DOCKER_PASSWORD} && sudo docker push shadabshah1680/multi-server:\${commit_id}"
				    sh "sed -i \"s|colour|blue|g\" qa-service.yaml && scp qa-service.yaml ubuntu@172.31.91.46:/tmp && ssh ubuntu@172.31.91.46 \"sudo kubectl apply -f /tmp/qa-service.yaml\""	
					input "Ready to change redirect traffic to blue?"
					sh 	"scp blue-replication-controller.yaml ubuntu@172.31.91.46:/tmp && ssh ubuntu@172.31.91.46 \"sudo kubectl apply -f /tmp/blue-replication-controller.yaml\""
					sh 	"scp blue-service.yaml ubuntu@172.31.91.46:/tmp && ssh ubuntu@172.31.91.46 \"sudo kubectl apply -f /tmp/blue-service.yaml\""
				}
				else
				{
				  sh "sed -i \"s|colour|green|g\" index.html && commit_id=`git rev-parse --short HEAD` && sudo docker build -t shadabshah1680/multi-server:\${commit_id}  -f Dockerfile . && sed -i \"s|latest|\${commit_id}|\" green-replication-controller.yaml && sed -i \"s|latest|\${commit_id}|\" green-replication-controller.yaml"
				  sh "commit_id=`git rev-parse --short HEAD` && name=`aws ssm get-parameter --name docker-username --query \'Parameter.Value\' --region us-east-1 --output text` && DOCKER_PASSWORD=`aws ssm get-parameter --name docker-password --query \'Parameter.Value\' --region us-east-1 --output text` && sudo docker login -u \${name} -p \${DOCKER_PASSWORD} && sudo docker push shadabshah1680/multi-server:\${commit_id}"
				  sh "sed -i \"s|colour|green|g\" qa-service.yaml && scp qa-service.yaml ubuntu@172.31.91.46:/tmp && ssh ubuntu@172.31.91.46 \"sudo kubectl apply -f /tmp/qa-service.yaml\""
				  input "Ready to change redirect traffic to green?"
				  sh 	"scp green-replication-controller.yaml ubuntu@172.31.91.46:/tmp && ssh ubuntu@172.31.91.46 \"sudo kubectl apply -f /tmp/green-replication-controller.yaml\""
				  sh 	"scp green-service.yaml ubuntu@172.31.91.46:/tmp && ssh ubuntu@172.31.91.46 \"sudo kubectl apply -f /tmp/green-service.yaml\""


				} 
				
			} 
			catch( Exception ee) 
			{ 
				echo 'pass'
			} 
    }
	stage('Clean Ws') {
			
					cleanWs()
		
	}
	




// pipeline {
//     agent {
//         label 'docker_labelnode_number'
//     }
// 	stages {
// 		stage('Clone Repo') {
// 			steps {
// 					sh "git clone -b main https://github.com/shadabshah1680/k8s-blue-green.git"
// 				}
// 			}
// 		stage('Build Docker Image') {
// 			steps {
				
// 					sh "sed -i \"s|Blue|red|g\" index.html && commit_id=`git rev-parse --short HEAD` && sudo docker build -t shadabshah1680/multi-server:\${commit_id}  -f Dockerfile . && sed -i \"s|latest|\${commit_id}|\" green-replication-controller.yaml && sed -i \"s|latest|\${commit_id}|\" green-replication-controller.yaml"
// 				}
// 			}
		
// 		stage('Upload Image To Dockerhub') {
// 			steps {
// 					sh "commit_id=`git rev-parse --short HEAD` && name=`aws ssm get-parameter --name docker-username --query \'Parameter.Value\' --region us-east-1 --output text` && DOCKER_PASSWORD=`aws ssm get-parameter --name docker-password --query \'Parameter.Value\' --region us-east-1 --output text` && sudo docker login -u \${name} -p \${DOCKER_PASSWORD} && sudo docker push shadabshah1680/multi-server:\${commit_id}"
// 				}
// 			}
// 		stage('Blue replication controller') {
// 			steps {
// 					sh 	"sudo kubectl apply -f ./k8s-blue-green/blue-replication-controller.yaml"
// 				}
// 			}
		
// 		stage('Green replication controller') {
// 			steps {
// 					sh "sudo kubectl apply -f ./k8s-blue-green/green-replication-controller.yaml"
// 			}
// 		}
// 		stage('Create the service in kubernetes cluster traffic to blue controller') {
// 			steps {
// 					sh 	"sudo kubectl apply -f ./k8s-blue-green/blue-service.yaml"
// 			}
// 		}
// 		stage('User approve to continue') {
//             steps {
//                 input "Ready to change redirect traffic to green?"
//             }
//         }
// 		stage('Create the service in kubernetes cluster traffic to green controller') {
// 			steps {
// 					sh	"sudo kubectl apply -f ./k8s-blue-green/green-service.yaml"
// 			}
// 		}
// 		stage('Clean Ws') {
// 			steps {
// 					cleanWs()
// 			}
// 		}
// 	}
}