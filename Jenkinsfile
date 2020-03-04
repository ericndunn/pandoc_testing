pipeline {
    agent none
    stages {
        stage('Checkout repository') {
            agent {
                label 'windows'
            }
            stages {
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
                stage('Convert Markdown files-1') {
                    steps {
                        powershell script: '''
                        try
                        {
                            Set-Location .\\DevOps-Pipeline\\DevOps-Pipeline-Process-Documentation\\
                            Get-ChildItem -Recurse -Include *.md | foreach {
                                $html="$env:WORKSPACE\\build\\temp\\$($PSItem.Name.Replace('.md','.html'))"
                                pandoc $PSItem.Name -o $html
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
                // stage('Cleanup Workspace') {
                //     steps {
                //         step([$class: 'WsCleanup'])
                //     }
                // }
            }
        }
    }
}

