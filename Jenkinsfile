pipeline {
    agent any
    parameters{
        string(name: 'name', description: 'Enter information about name')
        string(name: 'version', description: 'Enter information about version')
        string(name: 'Change_Request_Number', description: 'Enter information about Change_Request_Number')
    }

    environment {
      NEWRELIC_API_KEY="18da074407138d38d610dc6812c819c24ebb4dc1c785619"  
      ENV="prod"
      PATH="$PATH:/usr/local/bin"
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
              if (params.Change_Request_Number == '') {
                error("Incorrect SNOW ticker number entered !!")
              }
            }
          }
        }


        stage('Show kubectl version') {
          steps {
              sh("vim --version")
      //       sh("kubectl version")
          }
        }

        stage('Run deploy script') {
          steps {
            script {
 //             sh("python36 bin/k8s/deploy.py -e cwt-${ENV} -v ${params.version} ${params.name}")
                echo("python36 bin/k8s/deploy.py -e cwt-${ENV} -v ${params.version} ${params.name}")
                sleep 10
                echo "Run script"
                
            }
          }
        }

        stage('Verify deploy script') {
          steps {
            script {
//                def statusCode = sh(script: "python -u bin/k8s/verify.py ${params.name} cwt-${ENV}", returnStatus: true)
//                echo ${statusCode}
//                if (statusCode != 0){
                  if (params.version != "0" ){
//                  sh label: "ROLLING-BACK DEPLOYMENT!", script: "kubectl --context cwt-${ENV} rollout undo deployment ${params.name}"
                  sh label: "ROLLING-BACK DEPLOYMENT!", script: "echo kubectl --context cwt-${ENV} rollout undo deployment ${params.name}"
                      error("Rolled back ${ENV}")
                }
                
            }
          }
        }

        stage('Running service deployment in SATO') {
          environment {
            ENV="sato"
            NEWRELIC_API_KEY=""
          }
          steps {
            script {
                def sato_svc_arr = "autocomplete-service cbr-proxy config configurator cwt-apache-sites cwt-idm-svc cwt-idm-ui cwt-login-services-svc cwt-property-anonimizer-svc cwt-togo-version-svc dal-service digital-itinerary-parser-service flight-alert-service flightdeatils-service hotels-service nodejs-flightalerts places pnr-receiver-service poll-service push-service resource-service scheduling-service storage-service trips users cwt-notifications-svc cwt-notifications-verify-task cwt-failed-notifications-requeuer" 
                if (sato_svc_arr.find(params.name)){
//                  sh label: "Running service deployment in SATO - Make sure you are testing SATO as well", script: '''
//                      python36 bin/components-db.py validatedeploy ${ENV} ${params.name} ${params.version} || true
//                      python36 bin/k8s/deploy.py -e cwt-${ENV} -v ${params.version} ${params.name}
                    sh label: "Running service deployment in SATO - Make sure you are testing SATO as well", script: '''
                        echo python36 bin/components-db.py validatedeploy ${ENV} ${params.name} ${params.version} || true
                        echo python36 bin/k8s/deploy.py -e cwt-${ENV} -v ${params.version} ${params.name}
                  '''
                  

//                  def statusCode = sh(script: "python -u bin/k8s/verify.py ${params.name} cwt-${ENV}", returnStatus: true)
//                  echo ${statusCode}
                  if (params.name != 0){
//                    sh label: "ROLLING-BACK SATO DEPLOYMENT!", script: "kubectl --context cwt-${ENV} rollout undo deployment ${params.name}"
                      sh label: "ROLLING-BACK SATO DEPLOYMENT!", script: "echo kubectl --context cwt-${ENV} rollout undo deployment ${params.name}"
                      error("Rolled back ${ENV}")
                  }
                }
             }
           } 

          }


       stage("Create cloudfront invalidation for cwt-webui") {
         when {
               expression { params.name == "cwt-webui" } 
               expression { params.name == "cwt-webui-old-home-page" } 
            }
         steps {
            script {
                withCredentials([string(credentialsId: "${params.name}-id", variable: 'CF_ID')]) {
                  echo("Running CDN invalidate")
                  sh label: "Running CDN invalidate", script: '''aws cloudfront create-invalidation --distribution-id ${CF_ID} --paths "/*"'''
                }
            }
          }

       }


    }
}


