pipeline {
    agent any

    environment {
        scannerHome = tool "sonar"
    }

    stages {

        stage('cleanws') {
            steps {
                cleanWs()
            }
        }

        stage('Code') {
            steps {
                git 'https://github.com/gopi-wp/python-code-library-app.git'
            }
        }

        stage('dependancy-check') {
            steps {
                dependencyCheck additionalArguments: '--scan ./ --disableYarnAudit --disableNodeAudit\'', nvdCredentialsId: 'owaps-cred', odcInstallation: 'dp-check'
                dependencyCheckPublisher pattern: 'dependency-check-report.xml'
            }
        }

        stage('CQA') {
            steps {
                withSonarQubeEnv('sonar') {
                    sh "${scannerHome}/bin/sonar-scanner -Dsonar.projectKey=library"
                }
            }
        }

        stage('Qualitygates') {
            steps {
                waitForQualityGate abortPipeline: false, credentialsId: 'sonar-cred'
            }
        }
        stage('image build') {
            steps {
               sh ' docker build -t authimage .
         }
       }
       stage('image scan') {
          steps {
              sh 'trivy image authimage'
         }
      } 
       stage('tag and push') {
          steps {
              script {
                    withDockerRegistry(credentialsId: 'docker-cred') {
                     sh 'docker tag authimage gopibrahmaiah/library:authimage'
                     sh 'docker push gopibrahmaiah/library:authimage'
                  }
             }
          }
        }
      }
    }
