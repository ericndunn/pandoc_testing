<div class="section">

### Introduction

<div id="kb_introduction" class="section">

This document displays the process used to move repositories from Azure
to BitBucket.

</div>

</div>

<div class="section">

### Instructions

<div id="kb_instructions" class="section">

              1.)  Create SNAP Request:

1.  -   Go to: <https://pspctprd.service-now.com/sp?sysparm_stack=no>\
        \
    -   Search for:  "Create a New Repository"\
        \
    -   Create the “Order” in accordance with the posted instructions on
        the ServiceNow Automation Portal (SNAP) request.\
        \
    -   **NOTE**:  The recommended name of the new Bitbucket repository
        (repos) should be the same as the Azure repository name.\
        \

![](./Untitled_files/1%20SNAP%20Request.jpg){width="1213" height="619"}

![](./Untitled_files/2%20SNAP%20Request%20Team%20Name%20Description.jpg){width="1094"
height="336"}

1.  -   A Bitbucket repository will be created automatically from the
        SNAP request.\
        \
    -   Go to:  <https://perspecta.okta.com/app/UserHome>\
        \
    -   Click on the Bitbucket image/icon.\
        \
    -   *Optional*:  Go directly to Bitbucket: 
        <https://bitbucket.ngendso.com/dashboard>\
        \

![](./Untitled_files/3%20BitBucket.jpg){width="672" height="383"}

              2.)  Migrate Azure Repository to Bitbucket repository\
\

-   -   Clone Bitbucket repository to local machine to establish
        connection:\
        \
    -   ***NOTE***:  Before cloning new repository from Bitbucket you
        must create a Personal Access Token (PAT.)\
        \

![](./Untitled_files/4%20Personal%20Access%20Tokens.jpg){width="1659"
height="247"}

3.  -   Go to
        <https://bitbucket.ngendso.com/plugins/servlet/access-tokens/manage>\
        \
    -   Click on “Create Token.”\
        \
    -   Fill out the Token Details and Permissions and then click
        “Create”\
         
        -   Projects = **Read\
            \
            **
        -   Repositores = **Admin\
            \
            **

![](./Untitled_files/5%20PAT%20Form.jpg){width="679" height="777"}

***                          NOTE***:  You will not be able to view this
token again. 

**                          COPY and SAVE** the token someplace where
you can retrieve for the following steps:\
\

![](./Untitled_files/6%20PAT%20Created.jpg){width="778" height="345"}

 

4.  -   The token (alpha/numeric sequence) takes the place of the user’s
        credentials password.\
        \
    -   ***NOTE***:  The first time the git clone operation is
        performed:

<!-- -->

5.  -   -   Create a **new local directory** to contain the migrated
            repository.  This will avoid any issues if there is already
            a cloned repository with the same name.\
            \
        -   CD to the **new local directory.\
            \
            **
        -   Copy the Git Clone URL from Bitbucket.\
            \
            -   Go to: <https://bitbucket.ngendso.com/dashboard>\
                \
            -   Select "Projects" from the top bar.\
                \
            -   Select your project.\
                \
            -   Select your repository.\
                \
            -   Select the Clone Icon from the left (top icon.)\
                \
            -   Copy the provided URL.\
                \

![](./Untitled_files/7%20BitBucket%20Clone%20in%20Sourcetree.jpg){width="632"
height="415"}

6.  -   -   -   Run “git clone
                https://bitbucket.ngendso.com/scm/dtas1/YOUR-REPOSITORY.git”
                on the local machine.\
                \
            -   A prompt for credentials should appear.\
                \
            -   User Name = <user.name@uspsector.com>\
                \
            -   Password = token\
                \
            -   **IF THERE IS A PROBLEM** or a credentials prompt did
                not display, see the “**Credentials Manager**”
                instructions on last page of this document.

<!-- -->

7.  -   A credentials prompt may or may not be displayed for the next
        steps.

 

              3.)  Read the following article on “How to Migrate Git
Repository with Branches and Commit History”\
\

-   -   <https://datanextsolutions.com/blog/how-to-migrate-git-repository-with-branches-and-commit-history/>

![](./Untitled_files/8%20Solution.jpg){width="570" height="814"}

9.  -   Open Git Bash in the root of the folder that contains all the
        repos (i.e. c:\\**new local directory**)\
        \
    -   Run the following Git commands\
        \
    -   \$ **git clone --mirror
        [https://url-of-the-source-git-repo.git](https://url-of-the-source-git-repo.git/)\
        \
        **
    -   ***NOTE***:  (Source = Azure repository credentials may be
        required (Azure credentials - User + Token)\
        \
    -   \$ cd to-your-source-git-repo-folder.git\
        \
    -   \$ **git remote set-url --push origin**
        [https://url-of-the-destination-git-repo.git](https://url-of-the-destination-git-repo.git/)\
        \
    -   ***NOTE***:  (Destination = Bitbucket.  If Authentication fails,
        then run the command a second time and then the credentials
        prompt should display.  Use Bitbucket credentials - User +
        Token.)\
        \
    -   \$ **git push –mirror**

-   Verify the new Bitbucket repository was populated with migrated code
    from Azure.

**Credentials Manager**

-   On the Windows Laptop, navigate to Start and search for “Credentials
    Manager.”\
    \
-   When Credentials Manager is open, click on “Windows Credentials.”\
    \
-   Find any …Bitbucket.ngendso.com… entry, click the arrow next to
    “entry”, and then remove the entry.

 

![](./Untitled_files/9%20Credentials%20Manager.jpg){width="1067"
height="825"}

</div>

</div>
