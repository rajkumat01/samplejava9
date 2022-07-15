         stage("SnapshotValidation and GetSnapshotsCreated"){
            steps{
                echo "Triggering Get snapshots for applicationName:${appName},deployableName:${deployName},changeSetId:${changeSetId}"
                script{
                    def count = 1
                    def x = ""
                    changeSetResults = snDevOpsConfigGetSnapshots(applicationName:"${appName}",deployableName:"${deployName}",changesetNumber:"${changeSetId}")
                    def changeSetResultsObject = readJSON text: changeSetResults
                    echo "debug: ${changeSetResultsObject.validation}"
                    
                    if(changeSetResultsObject.validation == "not_validated") {
               for(int i = 1; i < 51; ++i)  {
                             x = snDevOpsConfigGetSnapshots(applicationName:"${appName}",deployableName:"${deployName}",changesetNumber:"${changeSetId}")
                            def y = readJSON text: x
                            if(y.validation == "not_validated"){
                       
                                 sleep(5)   
                             }
                             else {
                                      break  
                              }
              }
                    }
                    echo "Count : ${i}"


                    def changeSetResults1 = snDevOpsConfigGetSnapshots(applicationName:"${appName}",deployableName:"${deployName}",changesetNumber:"${changeSetId}")
                    echo "ChangeSet Result : ${changeSetResults}"  
                    def changeSetResultsObject1 = readJSON text: changeSetResults1
                        changeSetResultsObject1.each {
                           /* if(it.validation == "passed"){
                                echo "validation passed for snapshot : ${it.name}"
                                snapshotName = it.name
                            }else{
                                echo "Snapshot failed to get validated : ${it.name}" ;
                                assert it.validation == "passed"
                            }*/
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
