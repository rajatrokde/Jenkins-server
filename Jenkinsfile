// pipeline {
//     agent any

//     environment {
//         AWS_DEFAULT_REGION = 'your-aws-region'
//         AWS_ACCOUNT_ID = 'your-aws-account-id'
//         IMAGE_REPO_NAME = 'your-image-repo-name'
//         IMAGE_TAG = 'your-image-tag'
//         REPOSITORY_URI = "${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_DEFAULT_REGION}.amazonaws.com/${IMAGE_REPO_NAME}"
//     }

//     stages {
//         stage('Login to AWS ECR') {
//             steps {
//                 script {
//                     sh """
//                     aws ecr get-login-password --region ${AWS_DEFAULT_REGION} | \
//                     docker login --username AWS --password-stdin ${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_DEFAULT_REGION}.amazonaws.com
//                     """
//                 }
//             }
//         }

//         stage('Cloning Git Repository') {
//             steps {
//                 checkout([$class: 'GitSCM', 
//                     branches: [[name: '*/master']], 
//                     doGenerateSubmoduleConfigurations: false, 
//                     extensions: [], 
//                     submoduleCfg: [], 
//                     userRemoteConfigs: [[credentialsId: 'your-credentials-id', url: 'https://github.com/sd031/aws_codebuild_codedeploy_nodeJs_demo.git']]
//                 ])     
//             }
//         }

//         stage('Building Docker Image') {
//             steps {
//                 script {
//                     dockerImage = docker.build("${IMAGE_REPO_NAME}:${IMAGE_TAG}")
//                 }
//             }
//         }

//         stage('Pushing Docker Image to ECR') {
//             steps {
//                 script {
//                     sh "docker tag ${IMAGE_REPO_NAME}:${IMAGE_TAG} ${REPOSITORY_URI}:${IMAGE_TAG}"
//                     sh "docker push ${REPOSITORY_URI}:${IMAGE_TAG}"
//                 }
//             }
//         }
//     }
// }
pipeline {
    agent any

    environment {
        KUBECTL_VERSION = "v1.29.0"
        ARGOCD_VERSION = "v2.10.0"
        NAMESPACE = "argocd" // Change if you installed Argo CD in a different namespace
    }

    stages {
        stage('Install kubectl') {
            steps {
                sh '''
                curl -LO "https://dl.k8s.io/release/${KUBECTL_VERSION}/bin/linux/amd64/kubectl"
                chmod +x kubectl
                sudo mv kubectl /usr/local/bin/
                kubectl version --client
                '''
            }
        }

        stage('Install Argo CD CLI') {
            steps {
                sh '''
                curl -sSL -o argocd "https://github.com/argoproj/argo-cd/releases/download/${ARGOCD_VERSION}/argocd-linux-amd64"
                chmod +x argocd
                sudo mv argocd /usr/local/bin/
                argocd version --client
                '''
            }
        }

        stage('Get Argo CD Admin Password') {
            steps {
                sh '''
                echo "Fetching Argo CD admin password..."
                kubectl get secret -n ${NAMESPACE} argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 --decode
                echo ""
                '''
            }
        }
    }
}
