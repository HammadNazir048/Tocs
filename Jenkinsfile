pipeline {
  agent any
  environment {
    AZURE_RESOURCE_GROUP = 'python-webap_group'  // Change to your Azure resource group name
    WEBAPP_NAME = "python-webap"                   // Change to your Azure Web App name
    PACKAGE_NAME = "my-flask-app-v1.0.zip"      // Change the package name as per your choice
  }
  stages {
    stage('Workspace Cleanup') {
      steps {
        // Clean the workspace before starting the build
        cleanWs(deleteDirs: true)  // Try using deleteDirs: true for immediate cleanup
        echo 'Cleaning workspace...'
      }
    }
    stage('Checkout Git Branch') {
      steps {
        git branch: 'main', credentialsId: 'github-credentials', url: 'https://github.com/HammadNazir048/Tocs.git'
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
          // Use Azure service principal for authentication
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
