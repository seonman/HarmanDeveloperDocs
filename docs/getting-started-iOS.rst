Getting Started Guide (iOS)
===========================

HKWirelessHD SDK supports both Objective-C and Swift. This document assumes that developer creates his/her app using the Swift language.

In the section, we will use the HKPage app as an example.


Project Setup with HKWireless (normal version)
----------------------------------------------

Include HKWirelessHDSDK into your project
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

- Add HKWirelessHDSDK to your project by dragging and dropping the HKWirelessHDSDK folder into the project navigator. Select “Create groups”, and click finish. By doing this, the include header path for HKWirelessHD SDK is added to your project. We need only the “include” folder, so you may remove the references to the other items in the folder. (Right click on any items you want to remove, select “Delete”, and then select “Remove References”.)

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


Using HKWirelessHD API
----------------------

All APIs can be accessed through a singleton object of HKWControlHandler. Only you have to do is obtain the singleton instance of HKWControlHandler and use it to invoke whatever API you want to call.

The code examples in this document are in Swift. and the function definition is in Objective-C. The APIs are compatible both in Swift and Objective-C.

Initialize HKWirelessHD Control Handler and start the Wireless Audio
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. code-block:: swift

	// Obtain the HKWControlHandler singleton instance
	let g_HWControlHandler: HKWControlHandler = HKWControlHandler.sharedInstance()

	// Initialize the HKWControlHandler and start wireless audio
	HKWControlHandler.sharedInstance().initializeHKWirelessController (licenseKey)

``initializeHKWirelessController()`` takes a string of license key as parameter. Every developer who signs up for Harman developer community will receive a license key. If you have not received it yet, then just use the license key specified in the sample code for temporary use until your own license key is available.

.. note:: 
	``initializeHKWirelessController()`` is a blocking call. It waits until the call successfully initializes the wireless audio network. If the phone device does not belong to a Wi-Fi network or if there is other app already using HKWirelessHD and running on the same device, then it will wait until the other app releases the HKWControlHandler. It would be nice to present a dialog to user before calling ``initializeHKWirelessController()`` to notice that the app will wait until HKWirelessHD network is available. 


Discovery and refreshing of available speakers in the Wi-Fi network
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

The status of speakers can be changed dynamically over time. And, whenever a speaker is turned off or on, the list of speakers available in the network should be refreshed. Especially, when you select speakers for playback, the speaker list and the status of each speaker should be updated with the latest information.

Basically, whenever there is any change on the speaker side, the newest information of the speaker is available from the device list maintained by HKWControlHandler. But, the update is initiated by speakers, and there is some latency until the latest information is reflected to the HKWControlHandler. So, if you need the latest information without latency, you would better refresh the speaker status regularly.

To force to update the status of speakers regularly, the SDK provides a pair of convenient APIs to refresh device status. One of the use cases of these functions are to present a screen of speaker list to user and show the current speaker information in real-time manner.

To start checking the status of devices regularly, use ``startRefreshDeviceInfo()``. To stop checking the status regularly, use ``stopRefreshDeviceInfo()``.

.. code-block:: swift

	// start to refresh devices ... 
	HKWControlHandler.sharedInstance().startRefreshDeviceInfo()
	
	// stop to refresh devices
	HKWControlHandler.sharedInstance().stopRefreshDeviceInfo()  

``startRefreshDeviceInfo()`` will refresh and update every 2 seconds the status of the devices in the current Wi-Fi network.

.. note:: 
	Even without calling ``startRefreshDeviceInfo()``, the speaker information will be updated whenever the information is updated on speaker side, but there is some latency until the newest information is reflected to HKWControlHandler.


Speakers and Groups
~~~~~~~~~~~~~~~~~~~

There are two ways to choose speakers to play on – one is to select a speaker from the global list of speakers maintained by the internal data structure, and the other is to select a speaker with a group (or room) index and the index of the speaker within the group. Note that in this document, the term group and room are used as the same meaning, that is, a set of speakers.

Selecting a speaker individually
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

**Select a speaker in the global list**

.. code-block:: swift

	// get the number of available speakers
	let deviceCount = HKWControlHandler.sharedInstance().getDeviceCount()
	
	// get the info of the first devices in the list
	var index = 0
	let deviceInfo = HKWControlHandler.sharedInstance().getDeviceInfoByIndex(index)

**Retrieve DeviceInfo with deviceId**

If you know the deviceId of a speaker, then you can retrieve the device information using ``findDeviceFromList()``.

.. code-block:: swift

	// get the number of available speakers
	var deviceId : ClongLong = …
	let deviceInfo = HKWControlHandler.sharedInstance().findDeviceFromList(deviceId)

Selecting a speaker from a group
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

A **Group** is defined by the group information of each speaker. That is, if a speaker has a group where it belongs to, then the group has the speaker as a member. So, as an example, if speaker A and speaker B have the same group of Group C, then Group C will have speaker A and speaker B as members. If speaker A changes the group as ‘Group D’, then Group C will have only speaker B, and Group D will have speaker A as a member.

**Get the number of groups available in the network**

.. code-block:: swift

	// get the number of groups
	var groupCount = HKWControlHandler.sharedInstance().getGroupCount()

**Get the number of devices in a group**

.. code-block:: swift

	// get the number of devices in the first group 
	var groupIndex = 0
	var deviceCount = HKWControlHandler.sharedInstance().getDeviceCountInGroupIndex(groupIndex)

Retrieve the information of a device
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

You can retrieve the information of a device (speaker) using DeviceInfo object. Please refer to DeviceInfo.h. The following is the list of information that DeviceInfo provides:


================== ==============  ======================================================= ====================================== ============
Attribute          Type in Swift   Description                                             Fixed/Variable                         Set by API
================== ==============  ======================================================= ====================================== ============
deviceId           CLongLong       the unique ID of the speaker                            Fixed (in manufacturing)               No
deviceName         String          the name of the speaker                                 Variable                               Yes
groupId            CLongLong       the unique ID of the group that the speaker belongs to  Variable (set when a group is created) No
groupName          String          the name of the group that the speaker belongs to       Variable (set when a group is created) Yes
modelName          String          the name of the Model of the speaker                    Fixed (in manufacturing)               No
ipAddress          String          the IP address as String                                Fixed (when network setup)             No
port               String          the port number                                         Fixed (when network setup)             No
macAddress         String          the mac address as String                               Fixed (in manufacturing)               No
volume             Int             the volume level value (0 to 50)                        Variable                               Yes
active             Bool            indicates if added to the current playback session      Variable                               Yes
wifiSignalStrength Int             Wi-Fi strength in dBm scale, -100 (low) to 0 (high)     Variable                               No
role               Int             the role definition (stereo or 5.1 channel)             Variable                               Yes
version            String          the firmware version number as String                   Fixed (when firmware update)           No
balance            Int             the balance value in stereo mode. -6 to 6, 0 is neutral Variable                               Yes
isPlaying          Bool            indicates whether the speaker is playing or not         Variable                               No
channelType        Int             the channel type: 1 is stereo.                          Variable                               Yes
isMaster           Bool            indicates if it is the master in stereo or group mode   Variable                               Yes  
================== ==============  ======================================================= ====================================== ============

As shown in the table above, some of the attributes can be set by the APIs. And some attributes change during the runtime, so the app should keep the latest value of the attributes by calling corresponding APIs or by callback functions.

The following is an example of retrieving some of attributes of a speaker information.

.. code-block:: swift
	
	let deviceInfo: DeviceInfo = HKWControlHandler.sharedInstance().getDeviceInfoFromTable(groupIndex, deviceIndex:deviceIndex)
	
	println("deviceName: \(deviceInfo.deviceName")
	println("groupName: \(deviceInfo.groupName")
	println("volume: \(deviceInfo.volume")
	println("deviceId: \(deviceInfo.deviceId")
	println("deviceActive: \(deviceInfo.active")
	println("deviceModel: \(deviceInfo.modelName")
	...

Change speaker name and group name
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

**Change speaker name**

Use ``setDeviceName()`` to change the speaker name. Note that you cannot set the device name by setting “deviceName” property value directly. The property is read-only.

