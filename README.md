# Automated Process Documentation

1. This repository is for version control of various automated process documents.

1. This repository will contain documents only in a text format (markdown) that can generate into other formats ( i.e. .html, .pdf, .docx )

1. **DO NOT STORE** the output from a text file (markdown) in this repository.

1. The directory structure hierachy **WILL** mirror the directory structure hierachy of the Knowledge base in [ServiceNow SNOW](https://pspctprd.servicenowservices.com/nav_to.do?uri=%2F$knowledge.do).

**The below BASH and Powershell commands will insert target (that opens a new web browser tab) into each link within an HTML page.**

**Example HTML**

```html
BEFORE
<a href="https://jenkins.io/doc/pipeline/tour/environment/">Click to see more examples of how to use Environment Variables</a>
AFTER
<a target="_blank" href="https://jenkins.io/doc/pipeline/tour/environment/">Click to see more examples of how to use Environment Variables</a>
```

**Example BASH Command**

```bash
sed -i 's|href="https|target="_blank" href="https|g' *.html
```

**Example Powershell Command**

```ps
(Get-Content -path .\replacedemo.txt -Raw) -replace 'href="https','target="_blank" href="https' | Set-Content -Path .\replacedemo.txt
```
