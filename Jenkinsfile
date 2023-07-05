pipeline {
    agent any
    
    environment {
        AZURE_RESOURCE_GROUP = 'azure-jenk_group'
        AZURE_WEBAPP_NAME = 'azure-jenk'
        AZURE_CREDENTIALS_ID = '1523195a-5e0d-4e7e-ba88-a168cdffd973'
    }
    
    stages {
        stage('Build') {
            steps {
                // Checkout source code from version control system
                checkout scm
                
                // Build the Java application using Maven
                sh 'mvn clean package'
            }
        }
        
        stage('Deploy') {
            steps {
                // Authenticate with Azure using the Azure CLI plugin and the configured service principal credentials
                withCredentials([azureServicePrincipal(credentialsId: "${AZURE_CREDENTIALS_ID}", variable: 'AZURE_CREDENTIALS')]) {
                    sh 'az login --service-principal -u $AZURE_CLIENT_ID -p $AZURE_CLIENT_SECRET --tenant $AZURE_TENANT_ID'
                }
                
                // Deploy the application to Azure Web App using Azure CLI plugin
                sh 'az webapp deploy --name "${AZURE_WEBAPP_NAME}" --resource-group "${AZURE_RESOURCE_GROUP}" --type war --src-path target/java-hello-world.war'
            }
        }
    }
}
