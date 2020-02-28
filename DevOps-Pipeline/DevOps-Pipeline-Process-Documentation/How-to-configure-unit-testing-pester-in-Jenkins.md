# How to configure unit testing (pester) in Jenkins

## Unit Testing

* Unit tests are performed by the developer and are typically focused on the code they’ve just created or modified. They provide feedback that the code works as they wanted it to. Unit tests are done in isolation. By isolating the test to run on just the code they’ve written, and not that of external modules, they can be assured any problems they find are with their code and not with external modules or functions.

* To achieve isolation, testing uses the concept of mocks. Within a test a mock is used instead calling the actual external function desired. For example, let’s say your script calls Test-Path to validate the existence of a file. Actually, calling the real Test-Path function could cause several issues in testing.

* First, the Test-Path cmdlet itself could have a bug in it, previously undiscovered or maybe just not known to the developer. Second, the developer could be assuming the path containing the file they are seeking will always exist as part of the test. Issues would certainly arise if the directory was to unexpectedly vanish. Finally, the drive containing the file may not be present or issues such as network connectivity or hardware failure could be causing problems.

* Should any of these issues occur, the developer is left not only trying to debug his own code, but also struggling with the many ways the Test-Path cmdlet could have failed.

* Mocks provide a way to remove external code, such as Test-Path, from the equation. As you will see later in this series, you can create a ‘fake’ or ‘mock’ version of Test-Path that would always return a true (or false). This removes the external call (in this case to Test-Path) as a possible vector for errors a developer might have to track down. The next article in the series will dive deeper into mocks.

## Unit testing may be performed by various tools for various coding languages.

* Go to: [Testing Coverage & Execution](https://docs.sonarqube.org/latest/analysis/coverage/) for analysis parameters related to test coverage and execution reports related to various other code languages.

### Powershell Unit Testing with Pester

* Pester is a test framework for PowerShell. It provides a language that allows you to define test cases, and the Invoke-Pester cmdlet to execute these tests and report the results.
  * Jenkinsfile Invoke Build Pester Testing Example

```powershell
stage('Invoke-BuildPesterTesting') {
    steps {
        catchError(buildResult: 'FAILURE', stageResult: 'FAILURE') {
            powershell script: '''
            try
            {
                $tempPath = Join-Path -Path $env:WORKSPACE -ChildPath \\Build\\Temp\\
                $outputPath = ( Join-Path -Path $tempPath -ChildPath PesterResults )
                if ( ( Test-Path -Path $outputPath ) -eq $false )
                {
                    $null = New-Item -Path $outputPath -ItemType Directory
                }
                .\\Build\\Pipeline\\Invoke-BuildPesterTesting.ps1 -OutputPath $outputPath -CodeCoverageThreshold 80
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
