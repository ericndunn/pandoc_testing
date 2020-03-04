pipeline {
    agent none
    stages {
        stage('Checkout repository') {
            agent {
                label 'windows'
            }
            stages {
                stage('Prep Environment HTML') {
                    steps {
                        powershell script: '''
                        try
                        {
                            $tempPath = Join-Path -Path $env:WORKSPACE -ChildPath \\Build\\html-files\\
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
                stage('Prep Environment DOCX') {
                    steps {
                        powershell script: '''
                        try
                        {
                            $tempPath = Join-Path -Path $env:WORKSPACE -ChildPath \\Build\\docx-files\\
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
                stage('Convert Markdown files HTML') {
                    steps {
                        powershell script: '''
                        try
                        {
                            Set-Location .\\DevOps-Pipeline\\DevOps-Pipeline-Process-Documentation\\
                            Get-ChildItem -Recurse -Include *.md | foreach {
                                $html="$env:WORKSPACE\\build\\html-files\\$($PSItem.Name.Replace('.md','.html'))"
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
                stage('Convert Markdown files DOCX') {
                    steps {
                        powershell script: '''
                        try
                        {
                            Set-Location .\\DevOps-Pipeline\\DevOps-Pipeline-Process-Documentation\\
                            Get-ChildItem -Recurse -Include *.md | foreach {
                                $docx="$env:WORKSPACE\\build\\docx-files\\$($PSItem.Name.Replace('.md','.docx'))"
                                pandoc $PSItem.Name -o $docx
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

