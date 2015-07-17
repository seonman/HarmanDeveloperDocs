Troubleshooting (iOS)
=======================

Handling of link error “undefined symbols for architecture armv7”
------------------------------------------------------------------

HKWirelessHD SDK uses C++ codes, so the linker should include std c++ library. To prevent this kinf od link error, your project setting should include “-lstdc++” in Targets > Build Settings > Linking > Other Linker Flags field.

Unspecified linking parameter is added in link command
--------------------------------------------------------

If you encounter unspecified linking parameter such as library names, etc., there is possibility that Xcode is using cached build parameters that were used before. In this case, just delete “Xcode/DerivedData” folder in your ~/Library/Developer folder. That is, 

.. code-block:: swift

	$ cd ~/Library/Developer/Xcode/DerivedData/
	$ rm -rf [your-project-name]*

Playback stops when the app turns to background mode
-----------------------------------------------------

When an app playing music using HKWirelessHDSDK may stop playing when the app becomes background. It is because iOS stops all on-going network communication when the app is backgrounded. There are several expcetional cases that iOS allows even in background mode. Please refer to `iOS Developer Library`_ for more information on background execution.

.. _iOS Developer Library: https://developer.apple.com/library/ios/documentation/iPhone/Conceptual/iPhoneOSProgrammingGuide/BackgroundExecution/BackgroundExecution.html

To prevent our HKWirelessHD apps from stopping in background mode, we can do a trick based on iOS Audio background mode. For our sample apps not to be stopped during the background mode, we use `MMPDeepSleepPreventer`_. The idea of MMPDeepSleepPreventer is to play zero length of silent audio every 5 seconds by enabling iOS Audio background mode. 

.. _MMPDeepSleepPreventer: https://github.com/marcop/MMPDeepSleepPreventer

To enable iOS Audio background mode, you need to enable it in Project setting. 

- Go to Project > Targets > Capabilities > Background Modes
- Turn on the option of  **Audio and AirPlay**

.. figure:: img/troubleshooting/background-execution.png

Then, just initialize and start DeepSleepPreventer in ``AppDeleget.application:didFinishedLaunchingWithOption()`` as follows:

.. code-block:: swift

	func application(application: UIApplication, didFinishLaunchingWithOptions launchOptions: [NSObject: AnyObject]?) -> Bool {

		// prevent from turning into background
		sleepPreventer = MMPDeepSleepPreventer()
		sleepPreventer.startPreventSleep()

You can stop the sleep preventer when the app becomes foregrounded (``applicationWillEnterForeground()``), and start it again just before the app becomes backgrounded (``applicationWillResignActive()``).

	
Problem with iTunes Match and Apple Music
-------------------------------------------
Currently, HKWirelessHD SDK does not support Cloud-based streaming from iTunes Match or Apple Music. To play an audio file on Omni speakers, the audio file should be available on the device in advance.

And, audio file from Apple Music is DRM-enabled. So, it is not supported by HKWirelessHD SDK either. Only the audio files that you purchased or uploaded to iTunes Match by matching can be played on Omni speakers after they are downloaded on the phone.


MPMediaPicker not showing selection of items
---------------------------------------------
With iOS8.4, MPMediaPicker does not show the items selected by user. This symptom apears only with iOS8.4.  With ealier iOS version, such as iOS8.3 or before, the selected items turn grey color.
But in iOS8.4, the picker does not show any change even the an item is selected.  It seems a bug.

So, you need to be careful when you select items from MPMediaPicker. If you click on an item multiple times, the same iteam will appear on the Playlist the same multiple time as well.

Creating a simple HTTP server for music streaming
--------------------------------------------------
For testing ``PlayStreamingMedia()`` in HKWirelessHD iOS SDK or ``play_web_media`` command in REST API, you just need to run a simple HTTP server on your local PC or Mac. The following is a quick example for setup a HTTP Server for music streaming.

- Put your mp3 or wav files on a folder, e.g. **music**
- Install python on your PC or Mac (Mac has already python installed.)
- Run the followings:
	- $ python -m SimpleHTTPServer
	- Then, you will get some logs like this: Serving on 0.0.0.0 port 8000 ...
- Find the IP address of your PC or Mac. Let's say it is 172.20.10.3.
- Now you can access the musc file just like: http://172.20.10.3:8000/music/sample.mp3

Compiling and Running HKWPlayer App
--------------------------------------
HKWPlayer app cotains Apple Watch extension app inside. So, you can run the Watch app version of HKWPlayer app on your Apple Watch within the HKWPlayer app. A watch app is a kind of **Extension** app, and so we need to define a **App Group** so the main app and extension (watch) app can communicate with each other. 

**App Groups** is defined by following the Bundle ID. The Bundle ID of HKWPlayer app is currelty set as "com.harman.dev.hkwplayer". So, App Group id should be set as "group.com.harman.dev.hkwplayer", by adding "group" at the beginning. Currently, this bundle ID is used by Harman Developer Community team. So, you have to change this bundle ID for your use.

The following is how to change the bundle ID of the HKWPlayer app. First, change the bundle ID defined in App Groups. As example, let's say your bundle id is "com.myproject".

- Go to Targets > HKWPlayer > Capabilities > App Groups
	- Change the App Groups with "group.com.myproject"
- Go to Targets > HKWPlayer WatchKit Extension > Capabilities > App Groups
	- Change the App Groups with "group.com.myproject"

Now Change other parts of the codes that uses the bundle ID.

- To go **Search** menu in project navigator, and type "com.harman.dev"
	- You will see all the texts that contains the string.
- Click each item on the list, and then replace the stream with your own bundle ID, that is, "com.myproject"

The following is the screen capture of the list of the search.

.. figure:: img/troubleshooting/change-bundle-id.png
	:scale: 60


Please follow the instruction `Configuring App Groups`_ in iOS Developer Library for more information.

.. _`Configuring App Groups`: https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/AppDistributionGuide/AddingCapabilities/AddingCapabilities.html#//apple_ref/doc/uid/TP40012582-CH26-SW61

