# How to configure linting (code analysis) in Jenkins

* Linting or Code Analysis may be performed by various tools for various coding languages.

## Pipeline Team supports [SonarQube®](https://www.sonarqube.org/) tool.

* SonarQube® is an automatic code review tool to detect bugs, vulnerabilities and code smells in your code. It can integrate with your existing workflow to enable continuous code inspection across your project branches and pull requests.
  * SonarQube® static analysis is one place to measure the Reliability, Security, and Maintainability of all the languages in your project, and all the projects in your sphere.
  * Go to: [SonarScanner for Jenkins](https://docs.sonarqube.org/latest/analysis/scan/sonarscanner-for-jenkins/) for examples.
* Contact [Pipeline](mailto:ngendsopipelineteam@uspsector.com) team for more information on how to setup Code Analysis in Jenkins and SonarQube®.

## Powershell Code Analysis

[Go to: PowerShell Best Practices and Style Guide](https://pspctprd.servicenowservices.com/kb_view.do?sysparm_article=KB0011300) for Powershell standards.

### Powershell PSScriptAnalyzer

* PSScriptAnalyzer is a static code checker for Windows PowerShell modules and scripts. PSScriptAnalyzer checks the quality of Windows PowerShell code by running a set of rules. The rules are based on PowerShell best practices identified by PowerShell Team and the community. It generates DiagnosticResults (errors and warnings) to inform users about potential code defects and suggests possible solutions for improvements.
  * Jenkinsfile Invoke Build PSScript Analyzer Example

```powershell
stage('Invoke-BuildPSScriptAnalyzer') {
    steps {
        catchError(buildResult: 'FAILURE', stageResult: 'FAILURE') {
            powershell script: '''
            try
            {
                $tempPath = Join-Path -Path $env:WORKSPACE -ChildPath \\Build\\Temp\\
                $outputPath = ( Join-Path -Path $tempPath -ChildPath PSSAResults )
                if ( ( Test-Path -Path $outputPath ) -eq $false )
                {
                    $null = New-Item -Path $outputPath -ItemType Directory
                }
                .\\Build\\Pipeline\\Invoke-BuildPSScriptAnalyzer.ps1 -OutputPath $outputPath
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
```
