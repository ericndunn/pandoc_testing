# How to configure release (Jenkins/Artifactory)

## Artifactory Requirements

* Request [**Pipeline team**](mailto:ngendsopipelineteam@uspsector.com) to setup repositories in accordance with your development cycle.

    * ProjectName_DEV
    * ProjectName_TEST
    * ProjectName_PROD

## Jenkins requirements

### The following is the Jenkinsfile **Artifactory Plugin** syntax required to perform an artifact deployment to DEV repository in Artifactory:

[**Reference:** Uploading and Downloading Files in Artifactory](https://www.jfrog.com/confluence/display/RTF/Declarative+Pipeline+Syntax#DeclarativePipelineSyntax-UploadingandDownloadingFiles)

  * **rtBuildInfo** - It is important to place the **rtBuildInfo** closure before any steps associated with this build (for example, rtDownload and rtUpload), so that its configured functionality (for example, environment variables collection) will be invoked as part of these steps.
    * **maxBuilds** - Maximum builds to keep in Artifactory
  * **rtUpload** - Uploading files to Artifactory
  * **rtPublishBuildInfo** - publish the build-info to Artifactory
  * **serverId** property is used to reference pre-configured Artifactory server instance as described in the [Creating Artifactory Server Instance section](https://www.jfrog.com/confluence/display/RTF/Declarative+Pipeline+Syntax#DeclarativePipelineSyntax-CreatinganArtifactoryServerInstance).
  * **spec:** - File Spec can be defined.  See [Using File Spec](https://www.jfrog.com/confluence/display/RTF/Using+File+Specs).

~~~~
stage('Deploy artifact to DEV') {
    steps {
        rtBuildInfo (
            maxBuilds: 10
        )
        rtUpload (
            serverId: 'Artifactory NGEN DSO',
            spec: '''
            {
                "files": [
                    {
                        "pattern": ".\\Build\\Temp\\Packages\\*.nupkg",
                        "target": "OperationsOrchestration-ContentPack-WA_DEV"
                    }
                ]
            }
            '''
        )
        rtPublishBuildInfo ( serverId: 'Artifactory NGEN DSO' )
    }
}
~~~~

### The following is the Jenkinsfile syntax required to perform an artifact promotion from DEV to TEST in Artifactory:

[**Reference:** Promoting Builds in Artifactory](https://www.jfrog.com/confluence/display/RTF/Declarative+Pipeline+Syntax#DeclarativePipelineSyntax-PromotingBuildsinArtifactory)

* **rtPromote** - To promote a build between repositories in Artifactory.

~~~~
stage('Promote artifact to TEST') {
    steps {
        rtPromote (
            serverId: 'Artifactory NGEN DSO',
            sourceRepo: 'OperationsOrchestration-ContentPack-WA_DEV',
            targetRepo: 'OperationsOrchestration-ContentPack-WA_TEST',
            copy: true
        )
    }
}
~~~~

### The following is the Jenkinsfile syntax required to perform an artifact promotion From TEST to PROD in Artifactory:

~~~~
stage('Promote artifact to PROD') {
    steps {
        rtPromote (
            serverId: 'Artifactory NGEN DSO',
            sourceRepo: 'OperationsOrchestration-ContentPack-WA_TEST',
            targetRepo: 'OperationsOrchestration-ContentPack-WA_PROD',
            copy: true
        )
    }
}
~~~~




