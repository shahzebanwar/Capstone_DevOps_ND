pipeline {
    environment {
        registry = "shahzeb01/capstone-html"
        registryCredential = 'dockerhub'
        dockerImage = ''
    }
    agent any
    stages {

		    stage('Lint HTML') {
			    steps {
				    sh 'tidy -q -e *.html'
				    sh 'echo $USER'
			    }
		    }
        
    
            stage('Building image') {
                steps{
                script {
                    dockerImage = docker.build registry + ":$BUILD_NUMBER"
                    }
                }
            }

            stage('Deploy Image') {
                steps{
                script {
                    docker.withRegistry( '', registryCredential ) {
                    dockerImage.push()
                        }
                    }
                }
            }

            stage('Configure and Build Kubernetes Cluster'){
            steps {
                catchError(buildResult: 'SUCCESS', stageResult: 'FAILURE') {
                    sh 'ansible-playbook ./playbooks/create-cluster.yml'
                    }
                }
            }
            stage('Deploy Updated Image to Cluster'){
                steps {
                    catchError(buildResult: 'SUCCESS', stageResult: 'FAILURE') {
                    sh ' kubectl apply -f ./kube-cluster/deployment.yml'
					sh ' kubectl apply -f ./kube-cluster/loadbalancer.yml'
                }
            }
        }
    }
}