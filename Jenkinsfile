pipeline {
    agent any
    parameters{
        string(name: 'test_name', description: 'Enter information about name')
        string(name: 'version', description: 'Enter information about version')
        string(name: '', description: 'Enter information about version')
    }

    environment {
      PATH = '$PATH:/usr/local/bin'
    }

    stages {
        stage('Check version') {
            steps {
                script {
                    if (version == '') {
                        error("Incorrect version specified!")
                    }
                }
            }
        }

        stage('Check SNOW ticker number') {
          steps {
            script {
              if (Change_Request_Number == '') {
                error("Incorrect SNOW ticker number entered !!")
              }
            }
          }
        }


        stage('Show kubectl version') {
          steps {
            script {
              sh 'kubectl version'
            }
          }
        }

        stage('Run deploy script') {
          steps {
            script {
              sh 'python36 bin/k8s/deploy.py -e cwt-${ENV} -v ${version} ${name}'
            }
          }
        }

    }
}
