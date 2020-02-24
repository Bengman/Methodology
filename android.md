# Static Analysis

- [ ] Unpack the apk file.
```
apktool d <apk file>
sh d2j-dex2jar.sh com.package.name.apk
jd-gui
```

### Cryptography and authentication
- [ ] Check `AndroidManifest.xml` for juicy secrets.
- [ ] Check `/assets` and `/res/raw` for juicy secrets.
- [ ] Compile a list of interesting public APIs and create regular expressions to find keys or secrets through grep.
- [ ] Compile a list of interesting public APIs and look for their package names in apps.
- [ ] Either scan through the output manually file by file or run some searches within the extract folder for key works like ‘secret’, ‘password’, ‘user’, ‘token’, ‘api’, ‘key’, etc 
```
grep -Ri "secret" . | more

```
- [ ] Look for all uses of APIs in the javax.crypto package.
- [ ] Look for all embedded popular cryptography libraries.
- [ ] Audit initialization and use of discovered cryptography APIs.

- [ ] Search for OAuth endpoint URLs being sent to the browser and check what parameters are being passed
  - Implicit grant is always bad
  - If using Authz flow, check for PKCE use
  - If using PKCE, check that it is not predictable


### Insecure connections
- [ ] Review the app’s network security configuration (an Android P feature) to understand whether app is allowed to use insecure network protocols in the first place.
  - https://developer.android.com/training/articles/security-config (“Network
security configuration”)
- [ ] Grep the app’s code for “http:”, “ftp”, “smtp:”, and URLs that indicate use of insecure
network protocols.
- [ ] Understand common entry points into the network such as the URL class or the WebView class and check whether there are code flows that lead to insecure connections being made.
- [ ] In addition to the auditing tips for finding the use of insecure network protocols:
  - Understand which user data and application data is potentially sensitive
  - Audit whether any network connections over insecure protocols send potentially sensitive data to network endpoints
- [ ] In addition to the auditing tips for finding the use of insecure network protocols:
  - Understand which login information the app is working with
  - Audit whether any network connections over insecure protocols send login information to network endpoints

### Insecure storage
- [ ] Check for sensitive data on external storage (log files containing user info, user location data).
  - Check manifest for the permission `android.premission.WRITE_EXTERNAL_STORAGE`.
  - Grep within the code to see if the permission is used `getExternal`.
- [ ] Check for executable file on external storage such as apk, binary files etc.
- [ ] Check if the app is installing stuff from external storage. (binary code to sd card + installing package)
  - https://www.youtube.com/watch?v=OLgmPxTHLuY
- [ ] Look for files stored in internal storage whose access attributes are changed to make them world-accessible.
- [ ] Look for unsanitized file or path names that are supplied by potential attackers.
- [ ] Check for hidden directories on sd card or app drectory structure `ls -a`.

- [ ] Find any kind of path or file operations with variable input and see if they rely on user-provided input.
- [ ] Check for ZIP file traversal by finding all uses of APIs in the java.util.zip.* package or third-party zipping libraries.
- [ ] Understand whether user-provided ZIP files are being unzipped by the code.


### Find endpoints
- [ ] Search source code for more API / Web endpoints. `grep -hnrE "\.json" /Users/Gerben/smali/`.
- [ ] Run linkfinder.py on unpacked app to find even more endpoints
```
apktool d app.apk; cd app;mkdir collection; find . -name \*.smali -exec sh -c 'cp "$1" collection/$(head /dev/urandom | md5 | cut -d" " -f1).smali' _ {} \;; linkfinder.py -i 'collection/*.smali' -o cli
```

### Automated analysis
- [ ] Run the .apk in MobSF


# Communication to backend

- [ ] Proxy traffic through burp and test as API/webapp

### SSL Pinning

- [ ] Establish if the application is using SSL pinning.
(todo)


# Dynamic Analysis