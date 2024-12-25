pipeline {
  agent any
  environment {
    AZURE_RESOURCE_GROUP = 'python-webap_group'
    WEBAPP_NAME = "python-webap"
    PACKAGE_NAME = "python-ap-package.zip"
  }
  stages {
    stage('Workspace Cleanup') {
      steps {
        // Clean before build
        cleanWs()
        echo 'cleaning workspace...'
      }
    }
    stage('Checkout Git Branch') {
      steps {
        // Checkout the code from the second GitHub repository
        git branch: 'main', credentialsId: 'github-credentials', url: 'https://github.com/HammadNazir048/Tocs.git'
      }
    }
    stage('Build Application') {
      steps {
        // Install dependencies
        sh 'python3 -m pip install --upgrade pip'
        sh 'pip3 install -r requirements.txt'
      }
    }
    stage('Package Application') {
      steps {
        script {
          /* Zip all contents inside the code folder, excluding the root folder (code folder itself).*/
          sh "cd code && zip -r ../${PACKAGE_NAME} ./*"
          // Print the contents of the current directory to verify the zip
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
