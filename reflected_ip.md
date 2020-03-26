- [ ] If you see an IP displayed on the page, see if you can spoof it with the follwing headers:
```
X-Originating-IP: IP
X-Forwarded-For: IP
X-Remote-IP: IP
X-Remote-Addr: IP
X-Client-IP: IP
X-Host: IP
X-Forwared-Host: IP
```
- [ ] Also check if we could inject javascript that gets inserted in the page.
- [ ] Try submitting the header with underscores instead of hyphens. Some libraries will accept that. 
