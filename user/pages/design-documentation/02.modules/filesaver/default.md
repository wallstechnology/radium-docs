---
title: 'File Saver'
recaptchacontact:
    enabled: false
---

## File Saver

#### LIS API Functions
* dancerSaveFile - used for PHI files (reports, patient attachments, etc.) - saves file to the phi folder.
* dancerSavePublicFile - used for public files (lab logos, etc.) - saves file to the public folder.

#### Private Functions 
* getFileDownloadURL (table, filename) returns hash containing download_url
* deleteFile ( params: { file_dir || table, file_name, doc_name } ) returns hash containing msg and style.