pipeline {
    agent none
    stages {
        stage('Checkout repository') {
            agent {
                label 'core && windows_2016'
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
            }
            // post {
            //     cleanup {
            //         cleanWs deleteDirs: true, notFailBuild: true
            //     }
            // }
        }
    }
}
