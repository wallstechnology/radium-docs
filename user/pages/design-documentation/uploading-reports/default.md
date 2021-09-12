---
title: 'Uploading External Reports'
recaptchacontact:
    enabled: false
---

This is the design documentation for uploading reports.

## Summary
Some laboratories may want to outsource testing to reference labs and they need a way to upload reports from those reference labs into their system. There are a few ways that reports can be uploaded. Direct upload, SFTP upload, or Shared Drive upload. Both the SFTP and Shared Dr)ive options provide a streamlined way to upload reports. Simply drag and drop the reports into the folder and voila, they get uploaded. The caveat to this is that the reports must be named in such a way that it is obvious **HOW** to upload them.

## Proposed Solutions
### [Solution 1. Direct Upload](#sol1)
Users will be presented with a direct upload screen. They may upload multiple files, assign those files to orders, and add notes to the report. This provides a comprehensive design that eliminates any user "typing" errors associated with solution 2.

### [Solution 2. SFTP/Shared Drive Upload](#sol2)
This solution allows non-users to upload files by giving them sftp access to the server. This solution is not ideal because it requires additional configuration on the server side, rather than allowing the client to do the configuring. This solution is also vulnerable to typos: if a file name is not spelled correctly, the report doesn't get uploaded. A user may notice this and inform the uploader.

## Procedures and Screenshots
<a name="sol1"></a>
### Solution 1. Direct Upload 
![Upload Reports Mock Draft](https://docs.radiumlis.com/user/pages/images/UploadReportsPage.png)  
There is only one step with this method, and the user doesn't have to rename any files.

To create the solution for this, we will create a file upload form using the [FileSaver Module](../modules/filesaver).
There are two views:  
* file_uploader.tt - a basic template we can reuse for all file uploading needs
* upload_reports.tt  - the actual form which includes the file_uploader template  
  
  
We will use javascript to populate the form when the user selects files to upload.
The internal js files we will use are:
* [file_saver.js](../javascript/file_saver?target=_blank) - we will use `function documentFound` to set the title and value of the file uploader input.
* [form_options.js](../javascript/form_options?target=_blank)  - we will use `function addInputRow`  to copy our template div multiple times

<a name="sol2"></a>
### Solution 2. SFTP/Shared Drive Upload  
![Step 1. Rename the Report](https://docs.radiumlis.com/user/pages/images/Step1-RenameReport.png)  
Uploader must rename the report to the format: OrderID_ReportType.pdf
* ReportType can be any of the following: Screen,Confirmation,Reference

![Step 2. Move the file to the Reports Folder](https://docs.radiumlis.com/user/pages/images/Step2-MoveFileToReportsFolder.png)  
Step 2 involves moving the file to the reports folder. Drag and drop the file. Once this is done the system will pull the file in and insert it into the database.

## Findings
Solution 1 has less steps for the user and is less error prone because the system will be able to define which Order IDs are acceptable for entry and which Report Types are acceptable for entry.