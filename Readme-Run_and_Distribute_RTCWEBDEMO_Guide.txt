Running and Distribute RTCWEBDEMO Guide
Author: CPP
Date: 2017-04-11 11:53pm

Part Zero: CREATE PRJECT by npm
- 1. npm init [project name]
- 2. create package.json file
    - install dependencies: npm install
    - install react-native-webrtc: npm install react-native-webrtc
    - coding using sublime, create .js, .html files and so on.
- 3. make ios bundle: react-native bundle --platform ios --dev false --entry-file index.ios.js --bundle-output iOS/main.jsbundle


Part One: Run RTCWEBDEMO from Xcode

- 1. Version Preparation:
    - Node: v7.8.0
    - npm: v4.2.0
    - react-native-cli: 2.0.1
    - react-native: 0.40.0
    - Dependencies in Package.json:
        - "dependencies": {
        -     "react": "15.4.0-rc.4",
        -     "react-native": "^0.40.0",
        -     "react-native-webrtc": "git+https://git@github.com/oney/react-native-webrtc.git",
        -     "socket.io-client": "^1.3.7"
        -   }

- 2. In mac terminal
    - cd to an working directory or mkdir a new directory
    - git clone  https://github.com/cppiod/RCTWebRTCDemo.git
    - npm install
    - npm install react-native-webrtc --save
    - react-native bundle --platform ios --dev false --entry-file index.ios.js --bundle-output iOS/main.jsbundle
        Note: 0.10.0~0.12.0 required git-lfs, install Git Large File Storage (Git LFS) (if installed no need to install):
        - brew install git-lfs
        - git lfs install

- 3. Open the project in Xcode
    - Add File to Project (if existed in project navigator no need to add)
        - 1.) in Xcode: Right click Libraries ➜ Add Files to [project]
        - 2.) choose node_modules/react-native-webrtc/ios/RCTWebRTC.xcodeproj then Add
        - 3.) also add node_modules/react-native-webrtc/ios/WebRTC.framework to project root
    - Add library search path
        - 1.) select Build Settings, find Search Paths
        - 2.) edit BOTH Framework Sea
        rch Paths and Library Search Paths
        - 3.) add path on BOTH sections with: $(SRCROOT)/../node_modules/react-native-webrtc with recursive
        - 4.) set path on Header Search Path to
              $(inherited)  non-recursive
              /Applications/Xcode.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/include   non-recursive
              $(SRCROOT)/../node_modules/react-native/React  recursive
    - Change General Setting and Embed Framework
        - 1.) go to General tab
        - 2.) change Deployment Target to 8.0
        - 3.) add WebRTC.framework to Embedded Binaries
    - Link/Include Necessary Libraries
        - 1.) click Build Phases tab, open Link Binary With Libraries
        - 2.) add libRCTWebRTC.a
        - 3.) make sure WebRTC.framework linked

- 4. add the following libraries:
		AVFoundation.framework
		AudioToolbox.framework
		CoreGraphics.framework
		GLKit.framework
		CoreAudio.framework
		CoreVideo.framework
		VideoToolbox.framework
		libc.tbd
		libsqlite3.tbd
		libstdc++.tbd
    - Under Build setting set Dead Code Stripping to No also under Build Options set Enable Bitcode to No as well
    - Add two keys to info.plist in Xcode:
        - NSPhotoLibraryUsageDescription
        - NSCameraUsageDescription
    - Remember to sign the project under Target-> Signing -> select registered developer account
    - Build
        - Solving React/xxxx.h file not found problem by adding React to target and build: Product -> Edit Scheme -> Build, Uncheck Parallelize Build, Click add button, search React, add to targets and drag to top
    - Connect iPhone device to mac, unlock the device; select device, then build and run the project from Xcode, the project will be installed and tested on the device.
- 5. Testing
    - Run the project on device from Xcode
        - Connect your device to mac, select your device in Xcode
        - Build and Run the project from Xcode, on your device
    - Open signalling server from https://react-native-webrtc.herokuapp.com/ in browser as other peers

- 6. Clean Process
    - if you encounter any build time errors, like "linking library not found",
try the cleaning steps below, and do it again carefully with every steps.
        - remove npm module: rm -rf $YourProject/node_modules/react-native-webrtc
        - clean npm cache: npm cache clean
        - clear temporary build files ( depends on your env )
            - ANDROID: clear intermediate files in gradle buildDir
            - iOS: in xocde project, click Product -> clean
        - npm install react-native-webrtc

/////////////////////////////////////////////

Part Two: Distribution

    - Bundle Identifier setting
        - set bundle identifier in Targets->General->Bundle Identifier, input an identifier you make
        - copy the identifier to Info.plist->Bundle Identifier
    - Generate app icon
        - use https://makeappicon.com/ to generate different size icon pictures
        - in Xcode, click Images.xcassets in project navigator and drag dif size pics to apply
    - Registering App ID in developer.apple.com  (if distribute to app store, upload the app to iTunes.apple.com; if not, not needed)
        - Refer to this tutorial: https://www.youtube.com/watch?v=tnbOcpwJGa8&t=666s
        - use the same Bundle Identifier in Provisioning Profile -> Distribution -> Explicit App ID
    - Register testing device UDID in developer.apple.com
        - connect testing device to itunes
        - select Summary -> click on Serial Number to see UDID
        - login developer.apple.com and register UDID in Apple Developer Portal (add new device UDID under Devices -> All in developer.apple.com)
    - Archive:
        - Select Generic IOS Device
        - clean and build the project
        - Product -> Archive -> Export ipa files to your computer
            - Refer to App Distribution Guide: https://developer.apple.com/library/content/documentation/IDEs/Conceptual/AppDistributionGuide/MaintainingProfiles/MaintainingProfiles.html#//apple_ref/doc/uid/TP40012582-CH30-SW46
            - choose "Save for Development Deployment" and generate the ipa file to your computer
    - Install app through itunes
        - double click ipa file and open it in itunes
        - connect testing device to itunes
        - find the app in left app list and click install, click apply and done
        - the app will be installed on testing device.
    - Install app on your device using Xcode
        - window->devices->select your device -> Installed Apps -> click add button to select the ipa file from your computer
        
    注意：打包方式选择Save for Development Deployment；先在developer.apple.com添加udid，再archive打包成ipa，然后通过itunes安装。

    Aha! You can check the installed app through your device.

Part Three: Problems:

- Unable to resolve module react-native-webrtc from /Users/cpp/devworkspace/ReactNative/RCTWebRTCDemo/main.js: Module does not exist in the module map or in these directories: /Users/cpp/devworkspace/ReactNative/RCTWebRTCDemo/node_modules,   /Users/cpp/node_modules

This might be related to https://github.com/facebook/react-native/issues/4968
To resolve try the following:
  1. Clear watchman watches: `watchman watch-del-all`.
  2. Delete the `node_modules` folder: `rm -rf node_modules && npm install`.
  3. Reset packager cache: `rm -fr $TMPDIR/react-*` or `npm start -- --reset-cache`.

  - 试过clean process很多遍之后还是有这个错误，又试了rm -rf node_modules && npm install，之后rm -fr $TMPDIR/react-*，之后成功

///////////////////////////////////////
Note: 
Xcode version: Version 8.2.1 (8C1002)
Archived ipa from this Xcode version can't support ios above 10.2.0