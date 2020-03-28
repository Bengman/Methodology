- [ ] If you see an IP displayed on the page, see if you can spoof it with the follwing headers:
```
X-Originating-IP: 127.0.0.1
X-Forwarded-For: 127.0.0.1
X-Remote-IP: 127.0.0.1
X-Remote-Addr: 127.0.0.1
X-Client-IP: 127.0.0.1
X-Host: 127.0.0.1
X-Forwared-Host: 127.0.0.1
X-Real-IP: 127.0.0.1
Forwarded: for=127.0.0.1
```
- [ ] Also check if we could inject javascript that gets inserted in the page.
- [ ] Try submitting the header with underscores instead of hyphens. Some libraries will accept that. 
