pipeline {
    agent any
    
    parameters {
        choice(name: 'TERRAFORM_ACTION', choices: ['plan', 'apply', 'destroy'], description: 'Ação do Terraform a ser executada')
        string(name: 'WORKSPACE', defaultValue: 'dev', description: 'Workspace do Terraform (ambiente)')
        // string(name: 'TERRAFORM_DIR', defaultValue: './terraform', description: 'Diretório onde estão os arquivos Terraform')
    }
    
    environment {
        TF_IN_AUTOMATION = 'true'
        // Credenciais para o provedor de nuvem (exemplo para AWS)
        // AWS_CREDENTIALS = credentials('aws-credentials')
    }
    
    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }
        
        stage('Terraform Init') {
            steps {
                // dir("${params.TERRAFORM_DIR}") {
                    sh 'terraform init'
                    sh "terraform workspace select ${params.WORKSPACE} || terraform workspace new ${params.WORKSPACE}"
                }
            // }
        }
        
        stage('Terraform Validate') {
            steps {
                // dir("${params.TERRAFORM_DIR}") {
                    sh 'terraform validate'
                }
            // }
        }
        
        stage('Terraform Plan') {
            when {
                expression { params.TERRAFORM_ACTION == 'plan' || params.TERRAFORM_ACTION == 'apply' }
            }
            steps {
                // dir("${params.TERRAFORM_DIR}") {
                    sh "terraform plan -no-color"
                }
            // }
        }
        
        stage('Terraform Apply') {
            when {
                expression { params.TERRAFORM_ACTION == 'apply' }
            }
            steps {
                dir("${params.TERRAFORM_DIR}") {
                    sh "terraform apply -auto-approve -no-color"
                }
            }
        }
        
        stage('Terraform Destroy') {
            when {
                expression { params.TERRAFORM_ACTION == 'destroy' }
            }
            steps {
                // dir("${params.TERRAFORM_DIR}") {
                    sh "terraform destroy -auto-approve -no-color"
                }
            // }
        }
    }
    
    post {
        always {
            cleanWs()
        }
        success {
            echo "Pipeline executada com sucesso!"
        }
        failure {
            echo "Pipeline falhou!"
        }
    }
}
