
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
               sh ' docker build -t bookimage .
         }
       }
       stage('image scan') {
          steps {
              sh 'trivy image bookimage'
         }
      } 
       stage('tag and push') {
          steps {
              script {
                    withDockerRegistry(credentialsId: 'docker-cred') {
                     sh 'docker tag bookimage gopibrahmaiah/library:bookimage'
                     sh 'docker push gopibrahmaiah/library:bookimage'
                  }
             }
          }
        }
      }
    }
