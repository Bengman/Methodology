# IOS Methodology

- [ ] Jailbreak using Unc0ver


- [ ] Jailbreak using Checkra1n
  
  
 - [ ] Install iProxy for SSH over usb-cable
  - Back on your connected macOS devices, run: `brew install libusbmuxd (apt-get install libusbmuxd* for Linux)`
  - `iproxy 2222 22 (iproxy 2222 44 for Linux)`
  - In another terminal, run `ssh root@localhost -p 2222`
  - For the password, enter alpine

- [ ] Install Frida and Objection
  - On your macOS device, run `pip3 install frida-tools`
  - On your iPhone, open Cydia and add Frida’s repository by going to Sources → Edit → Add and enter `https://build.frida.re`
  - Go to Search → Enter Frida → Install
  - Back on your macOS device, run `pip3 install objection`
  - Finally, run `objection --gadget "com.apple.AppStore"` explore to check that everything is integrated properly
  
- [ ] Proxy Traffic and Bypass Cert Pinning
  - On Burp Suite, go to Proxy → Options → Proxy Listener → Add → Bind to port: 1337 → Bind to address : All interfaces (or select a Specific Address) → TLS Protocols → Use Custom Protocols → Uncheck TLSv1.3 → “OK”
  - On your iPhone, Settings → Wi-Fi → Info → Configure Proxy → Manual → Set server and port to the ones from the previous step
  - On your iPhone, go to `http://burp` → Click “CA Certificate” → Download profile → Settings → General → Profiles & Device Management → Portswigger CA → Install
  
- [ ] Bypass Cert-pinning with SSL Kill Switch 2
  - Make sure you have the following packages installed in Cydia: `wget, Debian Packager, Cydia Substrate, PreferenceLoader`
  - Go to the SSL Kill Switch 2 release page and copy the link to the latest .deb release
  - SSH into your iPhone (see the iProxy section above) and run `wget <RELEASE URL FROM STEP 2>` → `dpkg -i <DOWNLOADED PACKAGE NAME>` → `killall -HUP SpringBoard` → `rm <DOWNLOADED PACKAGE NAME>`
  - On your iPhone, go to Settings → SSL Kill Switch 2 (it should be at the bottom) → Disable Certificate Validation
  
- [ ] Bypass Jailbreak Detection with Liberty Lite
  - On your iPhone, open Cydia and add module author Ryley Angus’ repository by going to Sources → Edit → Add and enter `https://ryleyangus.com/repo/`
  - Go to Search → Enter Liberty Lite → Install
  - Once installed, go to Settings → Liberty → Block Jailbreak Detection → Enable for the app you want to bypass

- [ ] Extract .ipa files with frida-ios-dump
  - On your macOS device, `git clone https://github.com/AloneMonkey/frida-ios-dump.git && cd frida-ios-dump`
  - `sudo pip3 install -r requirements.txt --upgrade`
  - In another terminal, run `iproxy 2222 22` if it's not already running.
  - To dump an app's file, `./dump.py <APP DISPLAY NAME OR BUNDLE IDENTIFIER>`

- [ ] Extract .ipa files with ip installer
  - Install ipainstaller from Cydia
  - From a ssh prompt type `ipainstaller -l` to list all the apps installed on my jailbroken device and grab the bundle id of that app you wish to extract. Extract it using `ipainstaller -b <app_bundle>`.
  
- [ ] Install MobSF (Kali)
  - `git clone https://github.com/MobSF/Mobile-Security-Framework-MobSF.git`
  - `cd Mobile-Security-Framework-MobSF`
  - `./setup.sh`
  - Drag the file into MobSF

- [ ] App Decryption and class-dump
  
  
### References
- https://spaceraccoon.dev/from-checkra1n-to-frida-ios-app-pentesting-quickstart-on-ios-13
- https://www.allysonomalley.com/
- https://medium.com/@yogendra_h1/ios-application-security-jailbreak-12-4-5e3fc0dc0726
