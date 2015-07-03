Getting Started Guide (iOS)
===========================

HKWirelessHD SDK supports both Objective-C and Swift. This document assumes that developer creates his/her app using the Swift language.

In the section, we will use the HKPage app as an example.


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

