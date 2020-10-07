## Rooting Nexus 7(2013, flo) Android 6.0.1 from kali

- [ ] Install adb and fastboot `sudo apt install adb fastboot`
- [ ] Enable USB debugging. Go to Settings->About Tablet->Build Number. Tap on build number 7 times. This will enable the developer mode on your tab. You can see Developer Options in the Setting menu. Go in it and enable the USB Debugging option.

#### Unlocking bootloader
- [ ] Reboot your device in bootloader menu `adb reboot bootloader`
- [ ] Unlock the bootloader `sudo fastboot oem unlock`. Choose yes when the question to unlock appears.

#### Rooting using TWRP recovery image and CF Auto Root
- [ ] Download latest TWRP for device `https://eu.dl.twrp.me/flo/twrp-3.4.0-0-flo.img`
- [ ] Verify download hashsum `sha256sum twrp-3.4.0-0-flo.img`
- [ ] Download CF auto root for the specific device from firmware.mobi
- [ ] Verify download hashsum `sha1sum cfar_asus_nexus-7_razor_mob30x_i4hdt1.zip`
- [ ] Reboot your device in bootloader menu `adb reboot bootloader`
- [ ] Flash device with TWRP image `sudo fastboot flash recovery ~/Path-to-TWRP.img`
- [ ] Confirm by going to recovery mode by choosing it in the menu on the phone. You should now see the TWRP recovery mode menu.
- [ ] Install the root (CF Auto Root) while in fastboot mode `sudo fastboot boot ~/Path-To-The-Image/boot.img`



## Static Analysis

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
grep -Ri "secret" . --color=always | less -R
```

### Secrets:

https://github.com/streaak/keyhacks

Sensitive:

- `cloudinary://434762629765715:█████@reverb"` - // Cloudinary basic auth. "Note: You should only include the cloud_name in the value, the api secret and key should be left out of the application." https://hackerone.com/reports/351555
- `io.fabric.ApiKey` - https://hackerone.com/reports/766346
- `[key].apps.googleusercontent.com` - https://hackerone.com/reports/766346
- `Google maps api key` - https://github.com/streaak/keyhacks#Google-Maps-API-key

Non sensitive

- `CLS_ANDROID_SDK_DEVELOPER_TOKEN` - This is an internal identifier that the Firebase Crashlytics SDK uses 
- `assets/comScore.properties:PublisherSecret=` - The ComScore Publisher Secret is a text string used to obfuscate your application users' UDID when measurements are sent to comScore servers. This is required for security, and to protect the privacy of your application's users.
- `com_appboy_api_key` - probably safe
- `google_api_key` - This key is not sensitive
- `google_crash_reporting_api_key` - This is a standard configuration for Firebase products that get injected into your app as part of a standard app build using the Google Play services plugin.
- `qualaroo_api_key` - Probably not sensitive without the api secret.
- `APPSFLYER_DEV_KEY` - not sensitive

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

## Audit Content Providers
- [ ] 

## Audit Activities

- [ ] Check for exported sensitive activities without permissions (auth bypass).
- [ ] In addition to simply starting each exposed activity, you should
review the onCreate() method of each in search of conditional statements that
may lead to other code paths or unexpected behavior.
- [ ] Check if activites leak sensitive data through "Recent Application Screenshots".
- [ ] Check all exported activities that extend `PreferenceActivity` for the class named `isValidFragment()` to allow developers to override fragment injection protections and validate which fragments can be loaded inside the activity. Performing poor validation on the fragment name supplied to this method or simply returning true in this method without performing any checks would still result in fragment injection attacks being possible.

### Browser activities
Looking for an easy way to open arbitrary URLs in Android apps?
- [ ] Open AndroidManifest.xml
- [ ] Find all browser activities (must contain `<category android:name="android.intent.category.BROWSABLE"/>`)
- [ ] Run `adb shell am start -n app_package_name/component_name -a android.intent.action.VIEW -d http://google.com` for each of the activities (or any of your domains). Also track in Burp any requests to http://google.com or your domain.
- [ ] If a domain is opened, it means you found a vulnerability! Now inspect the request if it contains any auth tokens (if yes, it means you've got an account takeover!). No? Try different techniques to obtain any PII.
  - https://twitter.com/mem3hack/status/1294701158565634048


## Audit Services
- [ ] 

## Audit Broadcast recievers

## Audit Deep links
- [ ] Decompile an app with jadx
- [ ] Collect all deeplink handlers from AndroidManifest.xml, they look like `<data android:scheme="airbnb" android:host="d"/>`
- [ ] Grep among all sources and resources a pattern from a handler, in this case, `airbnb://d`
- [ ] You could find a lot of hardcoded urls like `airbnb://d/openurl?url=http://airbnb.com/blabla`. That's much simpler than learning app's sources
- [ ] Now try to put your own domains with adb `adb shell am start -a android.intent.action.VIEW -d airbnb://d/openurl?url=http://evil.com`, or on HTML pages (check out the H1 report below).
- [ ] Repeat the same thing for iOS apps. Usually, functionality is similar, but actual implementations are different
    - That's how I found https://hackerone.com/reports/401793 and earned $7.5k
    - https://twitter.com/_bagipro/status/1294972446135791616
 
 
## Audit Logging
- [ ] Check for logging of sensitive data `adb logcat`



## Audit attacksurface with Drozer

- [ ] Getting Drozer to work in latest Kali
```
wget https://github.com/FSecureLABS/drozer/releases/download/2.4.4/drozer-2.4.4-py2-none-any.whl
apt-get --assume-yes install python-pip
pip2 install wheel
pip2 install pyyaml
pip2 install pyhamcrest
pip2 install protobuf 
pip2 install pyopenssl 
pip2 install twisted
pip2 install service_identity
pip2 install drozer-2.4.4-py2-none-any.whl
```

- [ ] Install agent on device `$ adb install agent.apk`
- [ ] Forward server port to client `$ adb forward tcp:31415 tcp:31415`
- [ ] Connect to server `$ drozer console connect`
- [ ] Search for the application package `run app.package.list -f sieve`
- [ ] Show information about the package `run app.package.info -a com.mwr.example.sieve`
- [ ] Analyze app attacksurface `run app.package.attacksurface com.mwr.example.sieve`
- [ ] Show activities `run app.activity.info -a com.mwr.example.sieve `
- [ ] Interact with activities ` run app.activity.start --component com.mwr.example.sieve com.mwr.example.sieve.PWList`  
- [ ] Read from content providers `run app.provider.info -a com.mwr.example.sieve`
- [ ] Scan for URI's `run scanner.provider.finduris -a com.mwr.example.sieve`
- [ ] Scan for injections ` run scanner.provider.injection -a com.mwr.example.sieve`
- [ ] Interact with services `run app.service.info -a com.mwr.example.sieve`


## Audit Communication to backend

### Set up AP on Kali
- [ ] `apt-get install hostapd dnsmasq`
- [ ] Create `/etc/dnsmasq.conf`
```
# Bind to only one interface
bind-interfaces
# Choose interface for binding
interface=wlan0
# Specify range of IP addresses for DHCP leasses
dhcp-range=192.168.150.2,192.168.150.10
```
- [ ] Create `/etc/hostapd.conf`
```
# Define interface
interface=wlan0
# Select driver
driver=nl80211
# Set access point name
ssid=myhotspot
# Set access point harware mode to 802.11g
hw_mode=g
# Set WIFI channel (can be easily changed)
channel=6
# Enable WPA2 only (1 for WPA, 2 for WPA2, 3 for WPA + WPA2)
wpa=2
wpa_passphrase=mypassword
```
- [ ] Create `hotspot.sh`
```
#!/bin/bash
# Start
# Configure IP address for WLAN
sudo ifconfig wlan0 192.168.150.1
# Start DHCP/DNS server
sudo service dnsmasq restart
# Enable routing
sudo sysctl net.ipv4.ip_forward=1
# Enable NAT
sudo iptables -t nat -A POSTROUTING -o ppp0 -j MASQUERADE
# Run access point daemon
sudo hostapd /etc/hostapd.conf
# Stop
# Disable NAT
sudo iptables -D POSTROUTING -t nat -o ppp0 -j MASQUERADE
# Disable routing
sudo sysctl net.ipv4.ip_forward=0
# Disable DHCP/DNS server
sudo service dnsmasq stop
sudo service hostapd stop
```

- [ ] Proxy traffic through burp and test as API/webapp

### Bypass certificate pinning using Frida and Objection:
(if device = rooted)
- [ ] Install Objection on laptop `pip3 install objection`
- [ ] Install and run frida-server on device (as root)
```
$ adb root # might be required
$ adb push frida-server /data/local/tmp/
$ adb shell "chmod 755 /data/local/tmp/frida-server"
$ adb shell "/data/local/tmp/frida-server &"
```
- [ ] Start the app and find the process with `frida-ps -U`
- [ ] Connect with objection to the process `objection --gadget "process name" explore`
- [ ] Call function to disable cert pinning `android sslpinning disable`

(if device =! rooted):
- [ ] Install Objection on laptop `pip3 install objection`
- [ ] Patch apk `objection patchapk -2 -s app.apk`
- [ ] Install the new apk on device `adb install app.objection.apk`
- [ ] Check that frida finds gadgets `frida-ps -U` 
- [ ] Run the application on the device, it will pause at start up
- [ ] Connect to the debug interface of the app `objection explore`
- [ ] Call function to disable cert pinning `android sslpinning disable`

# Dynamic Analysis
