# Static Analysis

- [ ] Unpack the apk file.
```
apktool d <apk file>
sh d2j-dex2jar.sh com.package.name.apk
jd-gui
```

- [ ] Check `AndroidManifest.xml` for juicy secrets.
- [ ] Check `/assets` and `/res/raw` for juicy secrets.
- [ ] Either scan through the output manually file by file or run some searches within the extract folder for key works like ‘secret’, ‘password’, ‘user’, ‘token’, ‘api’, ‘key’, etc 
```
grep -Ri "secret" . | more

```

### Insecure storage
- [ ] Check for sensitive data on external storage (log files containing user info, user location data).
  - Check manifest for the permission `android.premission.WRITE_EXTERNAL_STORAGE`.
  - Grep within the code to see if the permission is used `getExternal`.
- [ ] Check for executable file on external storage such as apk, binary files etc.
- [ ] Check if the app is installing stuff from external storage. (binary code to sd card + installing package)
  - https://www.youtube.com/watch?v=OLgmPxTHLuY
- [ ] Check for hidden directories on sd card or app drectory structure `ls -a`.

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
