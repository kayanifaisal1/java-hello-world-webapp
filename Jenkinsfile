pipeline {
    agent any
    
    environment {
        AZURE_RESOURCE_GROUP = 'azure-jenk_group'
        AZURE_WEBAPP_NAME = 'azure-jenk'
        AZURE_CREDENTIALS_ID = '1523195a-5e0d-4e7e-ba88-a168cdffd973'
        AZURE_KEYVAULT_NAME = 'azurejenk'
        AZURE_SECRET_NAME = '1e10ad2b-6b30-4799-8347-a32a3515539b'
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
                withCredentials([azureKeyVault([
                    azureKeyVaultServiceEndpoint: "${AZURE_CREDENTIALS_ID}",
                    keyVaultName: "${AZURE_KEYVAULT_NAME}",
                    secrets: [
                        azureKeyVault(secretName: "${AZURE_SECRET_NAME}", variable: 'SECRET_VALUE')
                    ]
                ])]) {
                    // Authenticate with Azure using the Azure CLI plugin and the configured service principal credentials
                    sh 'az login --service-principal -u $AZURE_CLIENT_ID -p $AZURE_CLIENT_SECRET --tenant $AZURE_TENANT_ID'
                    
                    // Deploy the application to Azure Web App using Azure CLI plugin
                    sh 'az webapp deploy --name "${AZURE_WEBAPP_NAME}" --resource-group "${AZURE_RESOURCE_GROUP}" --type war --src-path target/java-hello-world.war --set appSettings.MY_SECRET="$SECRET_VALUE"'
                }
            }
        }
    }
}
