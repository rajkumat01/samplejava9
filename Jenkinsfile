    def appName='App_9'
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
        stage('Clone repository') {               
           steps{
                // checkout scm
                git branch: 'master', url: 'https://github.com/rajkumat01/samplejava9'
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
    }
    post {
        success {
            echo 'Run E2ESamplePipeLine9_1!'
            build job: 'E2ESamplePipeLine9_1', parameters: [string(name: 'MY_PARAM', value: 'value from Build pipeline')]
        }
    }
}
