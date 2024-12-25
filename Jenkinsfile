pipeline {
  agent any
  
    stages {
    stage('Checkout Git Branch') {
      steps {
        git branch: 'main', credentialsId: 'github-credentials', url: 'https://github.com/HammadNazir048/Tocs.git'
      }
  environment {
    AZURE_RESOURCE_GROUP = 'my-resource-group'  // Replace with your actual Azure resource group name
    WEBAPP_NAME = "my-webapp"                   // Replace with your actual Web App name
    PACKAGE_NAME = "my-flask-app-v1.0.zip"      // The name of your deployment package
    PATH = "/usr/bin/python3:$PATH"             // Add Python3 to PATH
  }

    }
    stage('Build Application') {
      steps {
        sh 'python3 -m pip install --upgrade pip'
        sh 'pip3 install -r requirements.txt'
      }
    }
    stage('Package Application') {
      steps {
        script {
          // Zip the contents inside the 'code' folder
          sh "cd code && zip -r ../${PACKAGE_NAME} ./*"
          // Print the contents of the zip file to verify
          sh "zipinfo ${PACKAGE_NAME}"
        }
      }
    }
    stage('Login to Azure') {
      steps {
        script {
          withCredentials([azureServicePrincipal('jenkins-pipeline-sp')]) {
            sh 'az login --service-principal -u $AZURE_CLIENT_ID -p $AZURE_CLIENT_SECRET -t $AZURE_TENANT_ID'
            // Deploy to Azure Web App
            sh 'az webapp deploy --resource-group ${AZURE_RESOURCE_GROUP} --name ${WEBAPP_NAME} --src-path "${WORKSPACE}/${PACKAGE_NAME}"'
          }
        }
      }
    }
  }
}
