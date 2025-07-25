pipeline {
    agent any
    environment {
        IMAGE = "sagar592/starbucks-app:${env.GIT_COMMIT}"
        KUBECONFIG = credentials('kubeconfig-credentials-id')
        AWS_ACCESS_KEY_ID = credentials('AKIAZL743X2CZNNYXFAW')
        AWS_SECRET_ACCESS_KEY = credentials('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')
    }
    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', 
                     url: 'https://github.com/harihshshyam/starbucks-kubernetes.git', 
                     credentialsId: 'github-token'
            }
        }
        stage('Build & Publish Docker') {
            steps {
                sh "docker build -t $IMAGE ."
                withDockerRegistry(credentialsId: 'dockerhub-credentials-id') {
                    sh "docker push $IMAGE"
                }
            }
        }
        stage('Deploy to EKS') {
            steps {
                sh "aws eks update-kubeconfig --name my-cluster --region ap-south-1"
                sh "kubectl --kubeconfig $KUBECONFIG set image deployment/my-app my-app=$IMAGE"
                sh "kubectl --kubeconfig $KUBECONFIG rollout status deployment/my-app"
            }
        }
    }
    post {
        always {
            echo 'Pipeline finished'
        }
    }
}
