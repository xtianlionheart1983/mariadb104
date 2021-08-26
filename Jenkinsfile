pipeline {
    agent any
	stages {
		stage('Pull Resources') {
			steps{
			    git url: 'ssh://git@github.com:xtianlionheart1983/mariadb104.git', branch: 'master', credentialsId: 'ssh-jenkins'
	
			}
		}
		stage('Build DB Image') {
			steps{
			    script{
				dockerImage = docker.build("xtianlionheart1983/gunsdb:gunsdbv1")
	    	}
    	}
	}
		stage('Push Image') {
			steps{
			    script{
				docker.withRegistry('https://registry.hub.docker.com', 'b066cebe-f6f5-48fa-8ba0-570c1cbc57ae') { 
                dockerImage.push("gunsdbv1")  
			    	}
			    }
		    }
	    }
	    stage('Create Deployment File') {
			steps{
			    sh '''

printf 'apiVersion: apps/v1
kind: Deployment
metadata:
  name: gunsdb
  labels:
    app: gunsdb
spec:
  replicas: 1
  selector:
    matchLabels:
      app: gunsdb
  template:
    metadata:
      labels:
        app: gunsdb
    spec:
      containers:
      - name: gunsdb
        image: xtianlionheart1983/gunsdb:gunsdbv1
        ports:
        - containerPort: 3306
        env:
          - name: MARIADB_ROOT_PASSWORD
            value: T3st@1234
          - name: MARIADB_DATABASE
            value: guns
---
kind: Service
apiVersion: v1
metadata: 
  name: gunsdb
  namespace: default
  labels:
    app: gunsdb
spec:
  ports:
    - name: tcp-3306
      protocol: TCP
      port: 3306
      targetPort: 3306
  selector:
    app: gunsdb
  type: LoadBalancer
'>gunsdb-deployment.yaml
'''
                    }
                }
            stage('Deploy DB') {
			steps{
				sh '''
				kubectl apply -f gunsdb-deployment.yaml
				'''
	    	 
        	}
	    }
    }
}
        
