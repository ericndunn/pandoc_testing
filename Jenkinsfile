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
                stage('Convert Markdown files') {
                    steps {
                        powershell script: '''
                        try
                        {
                            # cd $env:WORKSPACE\\DevOps-Pipeline\\DevOps-Pipeline-Process-Documentation\\;
                            gci -r -i $env:WORKSPACE\\DevOps-Pipeline\\DevOps-Pipeline-Process-Documentation\\*.md |foreach{$html=$_.directoryname+"\"+$_.basename+".html";pandoc -f markdown -s $_.name -o $html};
                            ls
                            }
                        catch
                        {
                            Write-Output $PSItem
                            exit 1
                        }
                        '''
                    }
                }
            // }
            // post {
            //     cleanup {
            //         cleanWs deleteDirs: true, notFailBuild: true
            //     }
            }
        }
    }
 }