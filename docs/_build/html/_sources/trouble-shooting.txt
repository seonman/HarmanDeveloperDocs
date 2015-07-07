Troubleshooting
====================

Handling of link error “undefined symbols for architecture armv7”
------------------------------------------------------------------

HKWirelessHD SDK uses C++ codes, so the linker should include std c++ library. To prevent this kinf od link error, your project setting should include “-lstdc++” in Targets > Build Settings > Linking > Other Linker Flags field.

Unspecified linking parameter is added in link command
--------------------------------------------------------

If you encounter unspecified linking parameter such as library names, etc., there is possibility that Xcode is using cached build parameters that were used before. In this case, just delete “Xcode/DerivedData” folder in your ~/Library/Developer folder. That is, 

.. code-block:: swift

	$ cd ~/Library/Developer/Xcode/DerivedData/
	$ rm -rf [your-project-name]*

Playback stops when the app goes to background mode
----------------------------------------------------

When an app playing music using HKWirelessHDSDK may stop playing when the app becomes background. It is because iOS stops all on-going network communication when the app is backgrounded. There are several expcetional cases that iOS allows even in background mode. Please refer to `iOS Developer Library`_ for more information 

.. _iOS Developer Libbrary: https://developer.apple.com/library/ios/documentation/iPhone/Conceptual/iPhoneOSProgrammingGuide/BackgroundExecution/BackgroundExecution.html

