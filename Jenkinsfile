pipeline {
  agent {
    docker {
      image 'python:3.9'  // Use the Python Docker image
    }
  }
  environment {
    AZURE_RESOURCE_GROUP = 'my-resource-group'
    WEBAPP_NAME = "my-webapp"
    PACKAGE_NAME = "my-flask-app-v1.0.zip"
  }
  stages {
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
          sh "cd code && zip -r ../${PACKAGE_NAME} ./*"
          sh "zipinfo ${PACKAGE_NAME}"
        }
      }
    }
    stage('Login to Azure') {
      steps {
        script {
          withCredentials([azureServicePrincipal('jenkins-pipeline-sp')]) {
            sh 'az login --service-principal -u $AZURE_CLIENT_ID -p $AZURE_CLIENT_SECRET -t $AZURE_TENANT_ID'
            sh 'az webapp deploy --resource-group ${AZURE_RESOURCE_GROUP} --name ${WEBAPP_NAME} --src-path "${WORKSPACE}/${PACKAGE_NAME}"'
          }
        }
      }
    }
  }
}
