pipeline {
  
  agent {
    label 'linux-agent'
  }

  parameters {
  
    string (
      name: 'IMAGE_TAG',
      defaultValue: 'latest',
      description: 'Docker Image Tag'
    )

    choice (
      name: 'GIT_BRANCH',
      choices: ['master', 'develop', 'release'],
      description: 'Git branch to checkout'
    )

    booleanParam (
      name: 'RUN_TESTS',
      defaultValue: 'true',
      description: 'Run Maven tests'
    )

    booleanParam (
      name: 'RUN_CONTAINER',
      defaultValue: 'true',
      description: 'Run Docker container'
    )

    choice (
      name: 'DEPLOY_ENV',
      choices: ['DEV', 'QA', 'PROD'],
      description: 'Deployment environment'
    )
  }

  environment {
    APP_NAME = 'onlinebookstore'
  }

  stages {

    stage ('Display Parameters') {
      steps {
          echo '========== BUILD PARAMETERS =========='
          echo "IMAGE_TAG     : ${params.IMAGE_TAG}"
          echo "GIT_BRANCH    : ${params.GIT_BRANCH}"
          echo "RUN_TESTS     : ${params.RUN_TESTS}"
          echo "RUN_CONTAINER : ${params.RUN_CONTAINER}"
          echo "DEPLOY_ENV    : ${params.DEPLOY_ENV}"
          echo "APP_NAME      : ${env.APP_NAME}"
          echo '======================================'
      }
    }

    stage ('Checkout') {
      steps {
        echo "Checking out branch: ${params.GIT_BRANCH}"

        git branch: params.GIT_BRANCH,
            url: 'https://github.com/shaikshafi07/onlinebookstore.git'
      } 
    }
  
    stage ('Maven Build') {
      steps {
        script {
          if (params.RUN_TESTS) {
              echo 'RUN_TESTS=true → Running Maven tests'
              sh 'mvn clean package'
          } else {
              echo 'RUN_TESTS=false → Skipping Maven tests'
              sh 'mvn clean package -DskipTests'
          }
        }
      }
    }

    stage ('Docker Build') {
      steps {
        script {
          def imageName = "${env.APP_NAME}:${params.IMAGE_TAG}"

          echo "Building Docker image: ${imageName}"

          sh "docker build -t ${imageName} ."
        }
      }
    }

    stage ('Run Container') {
      when {
        expression {
          params.RUN_CONTAINER
        }
      }

      steps {
        script {
          def imageName = "${env.APP_NAME}:${params.IMAGE_TAG}"

          echo "RUN_CONTAINER=true → Starting container"

          sh "docker run -d --name ${env.APP_NAME} ${imageName}"
        }
      }
    }

    stage ('Deploy DEV') {
      when {
        expression {
          params.DEPLOY_ENV == 'DEV'
        }
      }

      steps {
        echo "Deploying ${env.APP_NAME} to DEV environment"
      }
    }

    stage ('Deploy QA') {
      when {
        expression {
          params.DEPLOY_ENV == 'QA'
        }
      }

      steps {
        echo "Deploying ${env.APP_NAME} to QA environment"
      }
    }

    stage ('Deploy PROD') {
      when {
        expression {
          params.DEPLOY_ENV == 'PROD'
        }
      }

      steps {
        echo "Deploying ${env.APP_NAME} to PROD environment"
      }
    }
  }
}
