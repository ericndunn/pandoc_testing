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
                stage('Convert Markdown files1') {
                    steps {
                        powershell label: '', script: '''cd .\\DevOps-Pipeline\\DevOps-Pipeline-Process-Documentation\\;
                            gci -r -i *.md |foreach{$html=$_.directoryname+"\\"+$_.basename+".html";pandoc $_.name -o "$env:WORKSPACE\\Build\\Temp\\" $html}
                        '''
                    }
                }
                // stage('Convert Markdown files2') {
                //     steps {
                //         powershell script: '''
                //         try
                //         {
                //             cd .\\DevOps-Pipeline\\DevOps-Pipeline-Process-Documentation\\; gci -r -i .\\*.md |foreach{$html=$_.directoryname+"\"+$_.basename+".html";pandoc -f markdown -s $_.name -o $html};
                //             }
                //         catch
                //         {
                //             Write-Output $PSItem
                //             exit 1
                //         }
                //         '''
                //     }
                // }
                // stage('Git') {
                //     steps {
                //         step([$class: 'WsCleanup'])
                //     }
                // }
            }
        }
    }
}

