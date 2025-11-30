pipeline {
    agent any

    stages {

        stage('Build') {
            steps {
                echo 'Building Docker image...'
                sh 'docker-compose build'
            }
        }

        stage('Test') {
            steps {
                echo '🧪 Running container test...'
                sh 'docker run --rm quiz_activity_image:v1 echo "Container is working fine"'
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                echo 'Deploying to Kubernetes...'

                writeFile file: 'deployment.yaml', text: '''
apiVersion: apps/v1
kind: Deployment
metadata:
  name: quiz-activity-deployment
spec:
  replicas: 2
  selector:
    matchLabels:
      app: quiz-activity
  template:
    metadata:
      labels:
        app: quiz-activity
    spec:
      containers:
        - name: quiz-activity
          image: quiz_activity_image:v1
          ports:
            - containerPort: 5000
'''

                sh 'kubectl apply -f deployment.yaml'
            }
        }

        stage('Apply NodePort Service') {
            steps {
                echo '🧪 Applying nodeport service...'
                sh 'kubectl apply -f kubernetes/service.yaml'
            }
        }
    }
}
