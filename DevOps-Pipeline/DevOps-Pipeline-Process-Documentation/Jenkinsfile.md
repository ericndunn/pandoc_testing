# Jenkinsfile Example

* [*Click here for Official Pipeline documentation*](https://jenkins.io/doc/book/pipeline/)

###### *The following are examples taken from a Jenkins build with Powershell*

### Jenkinsfile (Declarative Pipeline)

* [*Click to see Jenkins Declarative Pipeline Tutorial*](https://digitalvarys.com/jenkins-declarative-pipeline-with-examples/)

~~~~
pipeline {
    options {
    buildDiscarder(logRotator(numToKeepStr: '12'))
    }
    agent none
    stages {
        stage('Checkout repository') {
            agent {
                label 'core && windows_2016'
            }
~~~~

### Example Environment Preparation

* [*Click to see more examples of how to use Environment Variables*](https://jenkins.io/doc/pipeline/tour/environment/)

~~~~
stage('Prep Environment') {
    steps {
        powershell script: '''
        try
        {
            $tempPath = Join-Path -Path $env:WORKSPACE -ChildPath \\Build\\Temp\\
            if ( ( Test-Path -Path $tempPath ) -eq $false )
            {
                New-Item -Path $tempPath -ItemType Directory -Force
            }
        }
        catch
        {
            Write-Output $PSItem
            exit 1
        }
        '''
    }
}
~~~~


### Example Powershell - Invoke-BuildOOProjectVersions

* [*Click to see Try-Catch Block, Scripted Pipeline*](https://jenkins.io/doc/book/pipeline/syntax/#flow-control)

~~~~
stage('Invoke-BuildOOProjectVersions') {
    when {
        branch 'master'
    }
    steps {
        powershell script: '''
        try
        {
            $tempPath = Join-Path -Path $env:WORKSPACE -ChildPath \\Build\\Temp\\
            .\\Build\\Pipeline\\Invoke-BuildOOProjectVersions.ps1
            $null = New-Item -Path "$tempPath\\Invoke-BuildOOProjectVersionsResults.txt" -ItemType File -Value (
                @{
                    updatesJson = "$env:updatesJson"
                    jsonTags = "$env:jsonTags"
                } | ConvertTo-Json -Compress
            )
        }
        catch
        {
            Write-Output $PSItem
            exit 1
        }
        '''
    }
}
~~~~

### Example Powershell - Install packages

~~~~
stage('Install NuGet Package Provider') {
    steps {
        powershell script: '''
        try
        {
            Install-PackageProvider nuget -Force -ErrorAction Stop
        }
        catch
        {
            Write-Output $PSItem
            exit 1
        }
        '''
    }
}
~~~~

### Example Powershell - with Conditional

* [*Click to see "when" condition*](https://jenkins.io/doc/book/pipeline/syntax/#when)

~~~~
stage('Invoke-BuildOOProjectArtifacts') {
    when {
        allOf {
            branch 'master'
            expression { currentBuild.currentResult == 'SUCCESS' }
        }
    }
    steps {
        powershell script: '''
        try
        {
            $tempPath = Join-Path -Path $env:WORKSPACE -ChildPath \\Build\\Temp\\
            .\\Build\\Pipeline\\Invoke-BuildOOProjectArtifacts.ps1 -UpdatesJson ( Get-Content -Path "$tempPath\\Invoke-BuildOOProjectVersionsResults.txt" | ConvertFrom-Json ).updatesJson
        }
        catch
        {
            Write-Output $PSItem
            exit 1
        }
        '''
    }
}
~~~~

### Example Powershell

~~~~
stage('Invoke-BuildOOProjectPackagePrep') {
    when {
        allOf {
            branch 'master'
            expression { currentBuild.currentResult == 'SUCCESS' }
        }
    }
    steps {
        powershell script: '''
        try
        {
            .\\Build\\Pipeline\\Invoke-BuildOOProjectPackagePrep.ps1
        }
        catch
        {
            Write-Output $PSItem
            exit 1
        }
        '''
    }
}
~~~~

### Example Powershell - Download executable

~~~~
stage('Download nuget.exe') {
    when {
        allOf {
            branch 'master'
            expression { currentBuild.currentResult == 'SUCCESS' }
        }
    }
    steps {
        powershell script: '''
        try
        {
            $tempPath = Join-Path -Path $env:WORKSPACE -ChildPath \\Build\\Temp\\
            $url = "https://dist.nuget.org/win-x86-commandline/v5.4.0/nuget.exe"
            $output = "$tempPath\\nuget.exe"
            $runTime = ( Measure-Command -Expression { Invoke-WebRequest -Uri $url -Method Get -OutFile $output -ErrorAction Stop } -ErrorAction Stop ).TotalSeconds
            Write-Output ( "Time taken: {0:n2} second(s)" -f $runTime )
        }
        catch
        {
            Write-Output $PSItem
            exit 1
        }
        '''
    }
}
~~~~

### Example Powershell - Running the executable

~~~~
stage('NuGet Pack') {
    when {
        allOf {
            branch 'master'
            expression { currentBuild.currentResult == 'SUCCESS' }
        }
    }
    steps {
        powershell script: '''
        try
        {
            $tempPath = Join-Path -Path $env:WORKSPACE -ChildPath \\Build\\Temp\\
            $output = Join-Path -Path $tempPath -ChildPath Packages
            $files = ( Get-ChildItem -Path "$output\\*" ).FullName
            if ( $null -ne $files )
            {
                foreach ( $file in $files )
                {
                    . "$tempPath\\nuget.exe" pack $file -NonInteractive -OutputDirectory $output
                }
            }
        }
        catch
        {
            Write-Output $PSItem
            exit 1
        }
        '''
    }
}
~~~~

### Example Final Commit

~~~~
stage('Final Commit') {
        when {
            allOf {
                branch 'master'
                expression { currentBuild.currentResult == 'SUCCESS' }
            }
        }
    steps {
        withCredentials([usernamePassword(credentialsId: '03146ed5-2b60-46dd-8760-9c45efa06cad', passwordVariable: 'pass', usernameVariable: 'uname')]) {
        powershell script: '''
            try
            {
                $Password = ConvertTo-SecureString $env:pass -AsPlainText -Force
                $Credential = New-Object System.Management.Automation.PSCredential ($env:uname, $Password)
                $tempPath = Join-Path -Path $env:WORKSPACE -ChildPath \\Build\\Temp\\
                .\\Build\\Pipeline\\Invoke-BuildPushCommit.ps1 -AdditionalTagsJson ( Get-Content -Path "$tempPath\\Invoke-BuildOOProjectVersionsResults.txt" | ConvertFrom-Json ).jsonTags -Credential $Credential
                $null = New-Item -Path .\\CommitResult.txt -ItemType File -Value $env:CommitStatus
            }
            catch
            {
                Write-Output $PSItem
                exit 1
            }
            '''
        }
    }
}
~~~~

### Example Check if Deployment is needed

~~~~
stage('Check if Deployment is needed') {
        when {
            allOf {
                branch 'master'
                expression { currentBuild.currentResult == 'SUCCESS' }
            }
        }
    steps {
        script {
            try {
                def CommitResult = readFile("CommitResult.txt")
                if (CommitResult == 'Abort Build') {
                    env.DeployStatus = "Stop"
                }
            }
            catch(Exception error) {
                return
                throw error
            }
        }
    }
}
~~~~

### Example Deploy artifact to Artifactory DEV repository

* [*Click to see *Uploading and Downloading files](https://www.jfrog.com/confluence/display/RTF/Declarative+Pipeline+Syntax#DeclarativePipelineSyntax-UploadingandDownloadingFiles)

~~~~
stage('Deploy artifact to DEV') {
    when {
        allOf {
            branch 'master'
            expression { currentBuild.currentResult == 'SUCCESS' }
            expression { env.DeployStatus != 'Stop' }
        }
    }
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

### Example Post step to clean the workspace

* [*Click to see "Post" step*](https://jenkins.io/doc/book/pipeline/syntax/#post)

~~~~
post {
    cleanup {
        cleanWs deleteDirs: true, notFailBuild: true, disableDeferredWipeout: true
    }
}
~~~~

### Example Input (YES or ABORT) artifact to Artifactory TEST repository

* [*Click to see "Input" directive*](https://jenkins.io/doc/book/pipeline/syntax/#input)

~~~~
stage('Promote artifact to TEST?') {
    when {
        allOf {
            branch 'master'
            expression { currentBuild.currentResult == 'SUCCESS' }
            expression { env.DeployStatus != 'Stop' }
        }
    }
    steps {
        input (
            message: 'Promote build artifacts to TEST?',
            ok: 'YES',
        )
    }
}
~~~~

### Example Promote artifact to Artifactory TEST repository

* [*Click to see how to promote a build between repositories in Artifactory*](https://www.jfrog.com/confluence/display/RTF/Declarative+Pipeline+Syntax#DeclarativePipelineSyntax-PromotingBuildsinArtifactory)

~~~~
stage('Promote artifact to TEST') {
    agent {
        label 'core && windows_2016'
    }
    when {
        allOf {
            branch 'master'
            expression { currentBuild.currentResult == 'SUCCESS' }
            expression { env.DeployStatus != 'Stop' }
        }
    }
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

### Example Input (YES or ABORT) artifact promotion to Artifactory PROD repository

~~~~
stage('Promote artifact to PROD?') {
    when {
        allOf {
            branch 'master'
            expression { currentBuild.currentResult == 'SUCCESS' }
            expression { env.DeployStatus != 'Stop' }
        }
    }
    steps {
        input (
            message: 'Promote build artifacts to PROD?',
            ok: 'YES',
        )
    }
}
~~~~

### Example Promote artifact to Artifactory PROD repository

~~~~
stage('Promote artifact to PROD') {
    agent {
        label 'core && windows_2016'
    }
    when {
        allOf {
            branch 'master'
            expression { currentBuild.currentResult == 'SUCCESS' }
            expression { env.DeployStatus != 'Stop' }
        }
    }
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
