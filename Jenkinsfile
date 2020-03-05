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
                stage('Copy HTML Images') {
                    steps {
                        powershell script: '''
                        try
                        {
                            Set-Location .\\DevOps-Pipeline\\DevOps-Pipeline-Process-Documentation\\
                            Copy-Item '.\\How-to-Move-Repositories-from-Azure-to-BitBucket' -Destination "$env:WORKSPACE\\Build\\html-files" -Recurse
                            Copy-Item '.\\How-to-setup-the-pre-post-merge-builds-in-jenkins_images' -Destination "$env:WORKSPACE\\Build\\html-files" -Recurse
                            Copy-Item '.\\Pipeline_Onboarding_Images' -Destination "$env:WORKSPACE\\Build\\html-files" -Recurse
                        }
                        catch
                        {
                            Write-Output $PSItem
                            exit 1
                        }
                            '''
                    }
                }
                stage('Replace strings') {
                    steps {
                        powershell script: '''
                        try
                        {
                            $InputFiles = Get-Item "$env:WORKSPACE\\Build\\html-files\\*.html"
                            $OldString  = 'href="https'
                            $NewString  = 'target="_blank" href="https'
                            $InputFiles | ForEach {
                                (Get-Content -Path $_.FullName).Replace($OldString,$NewString) | Set-Content -Path $_.FullName
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
                stage('Cleanup Workspace') {
                    steps {
                        step([$class: 'WsCleanup'])
                    }
                }
            }
        }
    }
}

