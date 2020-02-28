# Automated Process Documentation

1. This repository is for version control of various automated process documents.

1. This repository will contain documents only in a text format (markdown) that can generate into other formats ( i.e. .html, .pdf, .docx )

1. **DO NOT STORE** the output from a text file (markdown) in this repository.

1. The directory structure hierachy **WILL** mirror the directory structure hierachy of the Knowledge base in [ServiceNow SNOW](https://pspctprd.servicenowservices.com/nav_to.do?uri=%2F$knowledge.do).

## Initial Document Upload

```text
commands-to-render-html.md  DevOps-Pipeline/  README.md

DevOps-Pipeline:
DevOps-Pipeline-Process-Documentation/

DevOps-Pipeline/DevOps-Pipeline-Process-Documentation:
How-to-configure-artifact-generation-in-Jenkins.md
How-to-configure-linting-code-analysis-in-Jenkins.md
How-to-configure-release-Jenkins-Artifactory.md
How-to-configure-unit-testing-pester-in-Jenkins.md
How-to-setup-the-pre-post-merge-builds-in-jenkins.md
How-to-setup-the-pre-post-merge-builds-in-jenkins_images/
Jenkinsfile.md
Pipeline_Onboarding.md
Pipeline_Onboarding_Images/

DevOps-Pipeline/DevOps-Pipeline-Process-Documentation/How-to-setup-the-pre-post-merge-builds-in-jenkins_images/media:
add_source.JPG      BranchSource_2.JPG  configure_project.JPG
BranchSource_1.JPG  BranchSource_3.JPG

DevOps-Pipeline/DevOps-Pipeline-Process-Documentation/Pipeline_Onboarding_Images/media:
AttD40D.tmp.PNG  image1.jpeg  pipeline.jpg  pipeline.png
```

**The below BASH command will insert **target="_blank"** into each link within an HTML page. The inserted syntax with create links that open in a new tab in the browser.**

```bash
sed -i 's|href="https|target="_blank" href="https|g' *.html
```
