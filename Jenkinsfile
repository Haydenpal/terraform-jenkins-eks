pipeline {
    agent any
    tools {
        // Install the Terraform version configured as "terraform" and add it to the path.
        terraform "terraform"
    }

    
    stages {
        stage('Checkout SCM') {
            steps {
                script {
                    checkout scm
                }
            }
        }
        
        stage('Initializing Terraform') {
            steps {
                dir('EKS') {
                    sh 'terraform init'
                }
            }
        }

        stage('Formatting Terraform Code') {
            steps {
                dir('EKS') {
                    sh 'terraform fmt'
                }
            }
        }

        stage('Validating Terraform') {
            steps {
                dir('EKS') {
                    sh 'terraform validate'
                }
            }
        }

        stage('Previewing the Infra using Terraform') {
            steps {
                dir('EKS') {
                    sh 'terraform plan'
                    input(message: "Are you sure to proceed?", ok: "Proceed")
                }
            }
        }

        stage('Creating/Destroying an EKS Cluster') {
            steps {
                script {
                    dir('EKS') {
                        sh 'terraform apply --auto-approve' // Use "apply" to create/destroy resources
                    }
                }
            }
        }

        stage('Deploying Nginx Application') {
            steps {
                script {
                    dir('EKS/ConfigurationFiles') {
                        sh 'aws eks update-kubeconfig --name my-eks-cluster'
                        sh 'kubectl apply -f deployment.yaml'
                        sh 'kubectl apply -f service.yaml'
                    }
                }
            }
        }
    }
}

