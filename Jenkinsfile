  def appName='App_8'
    def snapName=''
    def deployName ='Dep_1'
    def exportFormat ='json'
    def configFilePath = "fileB"
    def fileNamePrefix ='exported_file_'
    def fullFileName="${appName}-${deployName}-${currentBuild.number}.${exportFormat}"
    def changeSetId=""
    def snapshotName=""
    def exporterName ='returnAllData' 

    //def namePath ="E2E/pipelineUpload/${currentBuild.number}"
    def namePath ='Comp_2'
    //def namePath ="Comp_2/${JOB_NAME}/${currentBuild.number}"
pipeline {
    agent any
    stages {
        
        stage('E2E_Pipeline1_1') {
            steps {
                echo "Running E2E_Pipeline1_1..........."
                echo "MY_PARAM=${env.MY_PARAM}"
            }
        }
        
        stage('Clone repository') {               
           steps{
                // checkout scm
                git branch: 'master1', url: 'https://github.com/rajkumat01/samplejava1'
           }
        }     
        stage('Upload JSON'){
            steps{
                script{
                    sh "echo validating configuration file ${configFilePath}.${exportFormat}"
                    echo "name path ::::: ${namePath}"
                    changeSetId = snDevOpsConfigUpload(applicationName:"${appName}",target:'component',namePath:"${namePath}", configFile:"fileB.json", autoCommit:true,autoValidate:true,dataFormat:"${exportFormat}")
                    // snDevOpsConfigUpload(applicationName:"${appName}",target:'deployable',namePath:"${namePath}", fileName:"deployable", autoCommit:'true',autoValidate:'true',dataFormat:"${exportFormat}",changesetNumber:"${changeSetId}", deployableName:"${deployName}")
                    echo "validation result $changeSetId"
                }
            }
        }
        stage("Committing the changeset"){
            steps{
                script{
                    echo "Change set registration for ${changeSetId}"
                    changeSetRegResult = snDevOpsConfigRegisterPipeline(changesetNumber:"${changeSetId}")
                    echo "change set registration set result ${changeSetRegResult}"
                }
            }
        }
      
      
       stage("SnapshotValidation and GetSnapshotsCreated"){
            steps{
                echo "Triggering Get snapshots for applicationName:${appName},deployableName:${deployName},changeSetId:${changeSetId}"
                script{
                    changeSetResults = snDevOpsConfigGetSnapshots(applicationName:"${appName}",deployableName:"${deployName}",changesetNumber:"${changeSetId}")
                    echo "ChangeSet Result : ${changeSetResults}"
                    def changeSetResultsObject = readJSON text: changeSetResults
                         changeSetResultsObject.each {
                           /* if(it.validation == "passed"){
                                echo "validation passed for snapshot : ${it.name}"
                                snapshotName = it.name
                            }else{
                                echo "Snapshot failed to get validated : ${it.name}" ;
                                assert it.validation == "passed"
                            } */
                            echo "validation passed for snapshot : ${it.name}"
                            snapshotName = it.name 
                        }
                  if (!snapshotName?.trim()){
                    error "No snapshot found to proceed" ;
                  }
                  echo "Snapshot Name : ${snapshotName} "  
                }
            }
        }
      
        stage('Publish the snapshot'){
            steps{
                script{
                    echo "Step to publish snapshot applicationName:${appName},deployableName:${deployName} snapshotName:${snapshotName}"
                    publishSnapshotResults = snDevOpsConfigPublish(applicationName:"${appName}",deployableName:"${deployName}",snapshotName: "${snapshotName}")
                    echo " Publish result for applicationName:${appName},deployableName:${deployName} snapshotName:${snapshotName} is ${publishSnapshotResults}"
                }
            }
        }


        stage('Deploy to the System'){
            steps{
                echo "Devops Change trigger change request"
                //snDevOpsChange()
            }

        }
        stage('Export Snapshot') {
            steps{
                script{
                    echo "Exporting for App: ${appName} Deployable; ${deployName} Exporter name ${exporterName} "
                    echo "Configfile exporter file name ${fullFileName}"
                    sh  'echo "<<<<<<<<<export file is starting >>>>>>>>"'
                    response = snDevOpsConfigExport(applicationName: "${appName}", snapshotName: "${snapshotName}", deployableName: "${deployName}",exporterFormat: "${exportFormat}", fileName:"${fullFileName}",exporterName: "${exporterName}")
                    //response = snDevOpsConfigExport(applicationName: "${appName}", snapshotName: "${snapName}", deployableName: "${deployName}",exporterFormat: "${exportFormat}", fileName:"${fullFileName}",exporterName: "${exporterName}")
                    echo " RESPONSE FROM EXPORT : ${response}"
                }
            }
        }
        stage("Deploying to PROD-US"){
            steps{
                echo "Reading config from file name ${fullFileName}"
                echo " ++++++++++++ BEGIN OF File Content ***************"
                sh "cat ${fullFileName}"
                echo " ++++++++++++ END OF File content ***************"
                echo "deploy finished successfully."
            }
        }
    }
    
 }
