# How to configure artifact generation in Jenkins

* Artifact generation configured in a Jenkinsfile differs for each and every method of the specific codebase.
* Maven, Gradle, Ivy, Java, Powershell, and NuGet all have different tools that generate deployable artifacts that can be stored and versioned in Artifactory
* The general method is to use the same Command-Line-Interface (CLI) commands within a Jenknsfile "stage".
* Plugins are available for specific source code builds.  Plugins for specific source code artifact generation must meet strict criteria to be enabled for Jenkins.  Contact [Pipeline](mailto:ngendsopipelineteam@uspsector.com) team for more information.

## Example NuGet artifact generation

~~~~
stage('NuGet Pack') {
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
