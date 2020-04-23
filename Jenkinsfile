pipeline{
    agent any
    stages{
        stage('Lint HTML'){
            steps {
                sh '''
					tidy -q -e *.html
					echo "Linting Test Done"
				'''
            }
        }
        stage('Build Docker Image') {
			steps {
				withCredentials([[$class: 'UsernamePasswordMultiBinding', credentialsId: 'DockerID', usernameVariable: 'DOCKER_USERNAME', passwordVariable: 'DOCKER_PASSWORD']]){
					sh '''
						docker build -t olutobi/capstone-project .
					'''
				}
			}
		}
        stage('Push Image To Dockerhub') {
			steps {
				withCredentials([[$class: 'UsernamePasswordMultiBinding', credentialsId: 'DockerID', usernameVariable: 'DOCKER_USERNAME', passwordVariable: 'DOCKER_PASSWORD']]){
					sh '''
						docker login -u $DOCKER_USERNAME -p $DOCKER_PASSWORD
						docker push olutobi/capstone-project
					'''
				}
			}
		}

		stage('Creating kubernetes cluster') {
			steps {
				withAWS(region:'us-west-2', credentials:'static') {
					sh '''
						curl --silent --location "https://github.com/weaveworks/eksctl/releases/latest/download/eksctl_$(uname -s)_amd64.tar.gz" | tar xz -C /tmp
						sudo mv /tmp/eksctl /usr/local/bin
						eksctl version
						eksctl create cluster --name capstone-project --version 1.13 \
												--nodegroup-name standard-workers \
												--node-type t2.micro \
												--nodes 2 \
												--nodes-min 1 \
												--nodes-max 3 \
												--node-ami auto \
												--region us-west-2 \
												--zones us-west-2a \
												--zones us-west-2b \
												--zones us-west-2c
					'''
				}
			}
		}

		stage('Create conf file cluster') {
			steps {
				withAWS(region:'us-west-2', credentials:'static') {
					sh '''
						aws eks --region us-west-2 update-kubeconfig --name capstone-project
					'''
				}
			}
		}

		stage('Set current kubectl context') {
			steps {
				withAWS(region:'us-west-2', credentials:'static') {
					sh '''
						kubectl config use-context arn:aws:eks:us-west-2:674408652160:cluster/capstone-project
					'''
				}
			}
		}

		stage('Deploy blue container') {
			steps {
				withAWS(region:'us-west-2', credentials:'static') {
					sh '''
						kubectl apply -f ./blue-controller.yml
					'''
				}
			}
		}

		stage('Deploy green container') {
			steps {
				withAWS(region:'us-west-2', credentials:'static') {
					sh '''
						kubectl apply -f ./green-controller.yml
					'''
				}
			}
		}

		stage('Create the service in the cluster, redirect to blue') {
			steps {
				withAWS(region:'us-west-2', credentials:'static') {
					sh '''
						kubectl apply -f ./blue-service.yml
					'''
				}
			}
		}

		stage('Wait user approve') {
            steps {
                input "Ready to redirect traffic to green?"
            }
        }

		stage('Create the service in the cluster, redirect to green') {
			steps {
				withAWS(region:'us-west-2', credentials:'static') {
					sh '''
						kubectl apply -f ./green-service.yml
					'''
				}
			}
		}

		stage('Details of Deployment') {
			steps {
				withAWS(region:'us-west-2', credentials:'static') {
					sh '''
						kubectl get pods
					'''
				}
			}
		}

	}
}
