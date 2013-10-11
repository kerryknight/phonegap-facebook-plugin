## If you're reading this, this is a work in progress--do not download plugin or follow along until this note is removed!

### Phonegap 3.0 CLI Automatic Installation of Apache Cordova Facebook Plugin for iOS

This updates the [official Apache Cordova Facebook Plugin](https://github.com/phonegap/phonegap-facebook-plugin) to get it installing locally with Phonegap CLI and Cordova 3.1/Phonegap 3.0. The current version of the the [official Apache Cordova Facebook Plugin](https://github.com/phonegap/phonegap-facebook-plugin) won't install automatically; this update resolves that with minor manual intervention.

### Note: This ONLY works with iOS currently
* I have removed all other platforms for the time being  

### Files Modified from the official plugin for this version:
* plugin.xml - to meet Phonegap 3.0+ plugin inclusion specs
* cdv-plugin-fb-connect.js - to be more in line with core Apache Cordova plugin design

1. IMPORTANT: I couldn't get phonegap or cordova to properly install this plugin with app id and app name parameters, so do not try to install this plugin prior to building the project with phonegap. We'll use plugman AFTER building with phonegap to install the plugin.

2. Within your Phonegap project's config.xml file, add an entry to include the plugin (Note: the feature tag's name parameter has been modified slightly from the official plugin's):
```xml
<feature name="com.phonegap.plugins.facebookconnect">
    <param name="ios-package" value="FacebookConnectPlugin" />
</feature>
``` 

3. Build your app with Phonegap CLI from the project directory: `phonegap build ios`

4. Your Phonegap project build should succeed with no errors. 

5. Now, install the plugin using plugman:
 <pre>plugman install --platform ios --project platforms/ios/ --plugin https://github.com/kerryknight/phonegap-facebook-plugin.git  --variable APP_ID=MyFBAppID --variable APP_NAME=MyFBAppDisplayName</pre>
 where MyFBAppID = your Facebook App ID and MyFBAppDisplayName = your Facebook App Display Name. You can find those on the Edit Settings page [here](https://developers.facebook.com/apps).

6. IMPORTANT: Before you can actually compile and run the project from within XCode, you'll need to make two very quick changes. If you try to compile it as is at this point, you'll get a "Could not load NIB in bundle:..." exception (I'm using XCode 5). This is due to two extraneous entries in the app's main .plist file that, for some reason, are being added in the build process with lots of whitespace in their <string> tags (I'm assuming the Phonegap CLI build process is doing this?). Even attempting to remove the whitespace with <config-file> tags in the plugin's plugin.xml didn't solve the issue. So, as a quick, manual fix (I'm sure those of you out there resourceful enough could work these entry deletions into your task runner of choice's workflow:)):

 Delete these two entries from the main app's *.plist file from plist view in Xcode:
 * Main nib file base name
 * Main nib file base name (iPad)

 Or, delete these two entries (and their empty string tags)from the main app's *.plist file using raw XML view:
  ```xml
  <key>NSMainNibFile</key>
  <key>NSMainNibFile~ipad</key>
  ``` 

7. You should now be able to build and run the app from within XCode.

8. Bonus: Aside from the FacebookConnectPlugin-related deprecations, XCode 5 is also adding a Dependency Analysis Warning: "Warning: no rule to process file...blah blah blah...FacebookSDK-3.5.2-Release.a' of type archive.ar....blah"

 Using the technique outlined [here](http://joytek.blogspot.tw/2011/09/xcode-4-warning-no-rule-to-process-file.html), you can fix this warning. It's literally as easy as going to your Project Target's Build Phases section, typing in "Facebook" in the search bar, and dragging the FacebookSDK-3.5.2-Release.a file from Compile Sources to Copy Bundle Resources. So far, this fix seems to work for me without issue.

9. Do a final project clean, build/run and Facebook away!

For any and all other documentation, please refer to the [official plugin's page](https://github.com/phonegap/phonegap-facebook-plugin). 




