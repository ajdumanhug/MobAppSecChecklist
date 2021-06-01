# MobAppSecChecklist
Don't mind this repo lol

## iOS

**Filesystem Analysis**
- [ ] The app IPA package can be found at the following location. `/private/var/containers/Bundle/Application/UUID/App.app` This contains all the resources that are shipped with the IPA and hence should always be checked for Sensitive Credentials.
- [ ] The Info.plist file is located at the following location. `/private/var/containers/Bundle/Application/UUID/App.app/Info.plist` The file contains all the details about the application and so much also be checked for hard coded secrets/credentials.
- [ ] All the application data is located at the following location. `/private/var/mobile/Containers/Data/Application/Data-UUID`This data location should be checked for local storage issues like storage of sensitive data in encrypted format and storing it in Plist files.
- [ ] No Protection (NSFileProtectionNone): No protection applied on file.
- [ ] Protected Until First User Authentication (NSFileProtectionCompleteUntilFirstUserAuthentication):File will remain accessible while the phone is unlocked and will remain unlocked until reboot.
- [ ] Protected Unless Open (NSFileProtectionCompleteUnlessOpen): The file will be unlocked when accessed and will be unlocked till reboot.
- [ ] Complete Protection (NSFileProtectionComplete): The file is unlocked when opened and then shorts after the device is locked the file is made inaccessible.

**Network Analysis**
- [ ] Check if the application does SSL pinning by adding burp CA and trusting it as a root CA.

**Application Issues**
- [ ] This data is located at `/var/mobile/Containers/Data/Application/UUID/Library/Caches/Snapshots/Sensitive` details should not get captured in the screenshot
- [ ] Sensitive data in iOS is recommended to be stored in the keychain, however there is no platform API to clear keychain data on app uninstall. This is called keychain persistence.
- The application should clear keychain items on logout like authentication token etc.
- To test install and use the app to populate the keychain and then uninstall the app.
- Reinstall the app to check if you can still access the app with the user login details.
- [ ] Text input is cached for UITextFields inputs and stored in the following location for language prediction which can cause leakage of sensitive data like credit card info. `/private/var/mobile/Library/Keyboard/dynamic-text.dat` Input data into the application at multiple places and see if it appears in the keyboard cache.
- [ ] URL schemes are a very good attack vector to your application and cause much damage.
- URL schemes are defined in the CFBundleURLTypes key of the Info.plist file of the app.
- Trace the usage of `*[* *openURL*]` selecter in the disassembly to understand the query parameters and the URL handling.
- Ensure URL parameters are properly validated and malformed URLs are discarded
- Ensure to only open URLs from trusted domains.
- Check if domain validation is properly implemented.
- [ ] UIWebVIew is deprecated since iOS 12 and is not recommended because JavaScript cannot be disable for it.
- [ ] SFSafariViewContruller is another common choice, JS is enabled by default and cannot be disabled and shared cookies and site data with Apple Safari.
- [ ] Verify if the app is allowing local files read via loaded WebView content by checking the value/s of loadFileURL:allowingReadAccessToURL:. in the disassembly of the app binary.
- [ ] Find any sensitive data which is protected by Biometric lock using the LocaAuthentication framework.
- [ ] Use the ios ui biometric_bypass from objection to check if we are able to bypass the biometric authentication.
- [ ] Many developers hard coded credentials in the app, search for secret, crypt, private, token etc.
- [ ] Many apps use third party backend systems, analyse and .json and .xml files for credentials.
- [ ] Many apps contain hidden features, search for develop, debug, fake, test in the app binary.
- [ ] To check logs connect the device to mac.
- [ ] Install the app on the jailbroken device if it is not showing any alert regarding the jailbroken device or not blocking the app to run. Then it is not protected with jailbreak detection
