# How to setup the pre/post Merge builds in Jenkins

## PRE Merge

### Bitbucket Requirements

* Read the following KB article ["How to Configure the Jenkins Webhook in Bitbucket"](https://pspctprd.servicenowservices.com/kb_view.do?sysparm_article=KB0012865)

  * This article explains how to configure the Jenkins Webhook in Bitbucket.  The webhook allows it to trigger builds in Jenkins and is configured for each repository.

* Create a Branch from the Master branch of your Bitbucket project.  This will allow you to work with your code without affecting the master branch.

### Jenkins Requirements

#### Set up Jenkins Project configuration with branch sources

* Login to Jenkins and click on the links that leads to your specific project:

  * Jenkins > TeamName > ProjectName

  * Click on the **Configure** link on the left side menu. (See image below)

![configure_project](.\How-to-setup-the-pre-post-merge-builds-in-jenkins_images\media\configure_project.JPG)

#### Click **Add source** > **Bitbucket** to start configuring the Bitbucket connections. (See image below)

![add_source](.\How-to-setup-the-pre-post-merge-builds-in-jenkins_images\media\add_source.JPG)

* The "Filter by name (with regular expression)" will allow Jenkins to create a master branch job. (See image below)

![BranchSource_1](.\How-to-setup-the-pre-post-merge-builds-in-jenkins_images\media\BranchSource_1.JPG)

* The "Discover pull requests from origin - Strategy" will allow Jenkins to create pull request jobs. (See image below)

![BranchSource_2](.\How-to-setup-the-pre-post-merge-builds-in-jenkins_images\media\BranchSource_2.JPG)

* This branch source "filter name ..." will allow Jenkins to create the specific Jenkins job from a specific branch in Bitbucket.  This optional configuration will allow you to test your build without affecting the master branch. (See image below)

![BranchSource_3](.\How-to-setup-the-pre-post-merge-builds-in-jenkins_images\media\BranchSource_3.JPG)

#### The steps in the Jenkinsfile are as follows: (See comments in code sections)

~~~~
stage('Final Commit') {
    // Use a when condition to only build from master branch
    when {
        allOf {
            branch 'master'
        }
    }
    steps {
        withCredentials([usernamePassword(credentialsId: '03146ed5-2b60-46dd-8760-9c45efa06cad', passwordVariable: 'pass', usernameVariable: 'uname')]) {
            // Use a try/catch block to allow Jenkins to fail if script fails.
            powershell script: '''
            try
            {
                $Password = ConvertTo-SecureString $env:pass -AsPlainText -Force
                $Credential = New-Object System.Management.Automation.PSCredential ($env:uname, $Password)
                $tempPath = Join-Path -Path $env:WORKSPACE -ChildPath \\Build\\Temp\\
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

#### This step was designed to check if a deployment is required. (See comments in code sections)

~~~~
stage('Check if Deployment is needed') {
        // Use multiple when conditions if you want to run if previous step was successful.
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
