Getting Started Guide (iOS)
===========================

HKWirelessHD SDK supports both Objective-C and Swift. This document assumes that developer creates his/her app using the Swift language.

There are two versions of SDK - a normal version (HKWirelessHDSDK) and a lightweight version (HKWirelessHDSDKlw). Most of the features are common in the two version. The only difference is that the normal version includes an API for playing web streaming audio, while the lightweight version does not.

The reason we support the SDK as two separate version is that we know that many developers want a feature of web streaming. To support this feature, we need to include a version of FFMPEG library inside of the SDK library. But, some developers may want to use their own version of MMPEG to handle audio stream for some particular reasons.

Please see the descriptions of each version below and make a proper choice for your app.

- HKWirelessHD (normal version) : support web streaming audio
	- Only support 32bit (armv7) architecture. armv7s and arm64 are not supported.
	- Support webstreaming music playback.
	- libz.dylib and libbz2.dylib are required when linking.
	
- HKWirelessHDlw (lightweight version) : do not support web streaming audio
	- Supports both 32bit (armv7, armv7s) and 64bit architecture (arm64)
	- Do not support web streaming music playback (streaming music from HTTP server, etc.)
	- No other library required
		
So, if you do not need web streaming music playback for your app, you may use HKWirelessHDlw (lightweight) version. Otherwise, you should use HHWirelessHD version.




In the section, we will use HKWSimple app as an example of normal version and HKPage app as an example of lightweight version.


Project Setup with HKWireless (normal version)
----------------------------------------------

Include HKWirelessHDSDK into your project
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

- Add HKWirelessHDSDK to your project by dragging and dropping the HKWirelessHDSDK folder into the project navigator.
- Select “Create groups”, and click finish. By doing this, the include header path for HKWirelessHD SDK is added to your project. 
- We need only the “include” folder, so you may remove the references to the other items in the folder. (Right click on any items you want to remove, select “Delete”, and then select “Remove References”.)

Add libHKWirelessHD.a as link binary
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

- Project Setting > Your Targets > Build Phases > Link Binary With Libraries 
	- Click '+', and then click "Add Other..."
	- Find 'libHKWirelessHD.a' in HKPage/HKWirelessHDSDK/lib, and add it.

.. figure:: img/getting-started-iOS/project-setting-1.png

After adding the HKWirelessHDSDK folder into your project and adding libHKWirelessHD.a, the project navigator will look like as below:

.. figure:: img/getting-started-iOS/project-setting-2.png

Add Swift Bridging Header
~~~~~~~~~~~~~~~~~~~~~~~~~

HKWirelessHD SDK has been written in C++ and Objective-C. Therefore, when you use the SDK in Swift, you should include Swift Bridging Header in your project. To do this:

1. Go to Project Setting > Build Setting > Swift Compiler - Code Generation > Objective-C Bridging Header

.. figure:: img/getting-started-iOS/project-setting-3.png

2. Add [My-Project-Name]/[My-Project-Name]-Bridging-Header.h

	- In our example, it looks like : HKPage/HKPage-Bridging-Header.h

3. Add the following lines in the header file.

.. code-block:: swift

	#import "HKWControlHandler.h"
	#import "HKWPlayerEventHandlerSingleton.h"
	#import "HKWDeviceEventHandlerSingleton.h"

Add -lstdc++ as linker flag
~~~~~~~~~~~~~~~~~~~~~~~~~~~

- Project Setting > Your Targets > Build Settings > Linking > Other Linder Flags
	- Add “-lstdc++” in the text field

.. figure:: img/getting-started-iOS/project-setting-4.png


Sample Application (HKWSimple)
-------------------------------

