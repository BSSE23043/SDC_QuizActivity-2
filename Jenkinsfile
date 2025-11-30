pipeline {
    agent any

    stages {

        stage('Build') {
            steps {
                echo 'Building Docker image...'
                sh 'docker build -t quiz_activity_image:v1 .'
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
            - containerPort: 80
'''

                sh 'kubectl apply -f deployment.yaml'
            }
        }

        stage('Apply NodePort Service') {
            steps {
                echo '🧪 Applying nodeport service...'
                sh 'kubectl expose deployment quiz-activity-deployment --type=NodePort --port=80 --target-port=80'
            }
        }

        stage('Apply Port Forwarding') {
            steps {
                echo '🧪 Applying port forwarding...'
                sh 'kubectl port-forward deployment/quiz-activity-deployment 8080:80'
            }
        }
    }
}
