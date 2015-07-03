Programming Guide
====================

In this chapter, we explain how to use HKWirelessHD APIs to create an app controlling HK Omni speakers. The sample codes explained in this section are copied from HKWPlayer app.

All APIs can be accessed through the singleton object pointer of HKWControlHandler. The first thing you have to do is create an instance of HKWControlHandler and use it to invoke the APIs you want to use.

In HKWirelessHD SDK, all the APIs are defined in Objective-C. However, the examples in this document are written in Swift. In case you use Swift for app development, you need to create a Swift bridging header file to make Objective-C APIs visible to Swift code.

For setting up a project with HKWirelessHD SDK, please refer to Getting Started Guide.


Initialization
-----------------



Initialize HKWirelessHD Control Handler and start the Wireless Audio
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Controlling HK Omni speakers are done by calling APIs provided by HKWControlHandler. So, the first thing to do to use HKWirelessHD APIs is to acquire the singleton object of HKWControlHandler and initialize it. 

Once you acquire the HKWControlHandler object, you need to initialize it by calling initializeHKWirelessController(). ``initializeHKWirelessController()`` takes a string of license key as parameter. Every developer who signs up for Harman developer community will receive a license key. If you have not received it yet, then just use the license key specified in the sample code for temporary use until your own license key is delivered to you.

.. code-block:: swift

	if HKWControlHandler.sharedInstance().isInitialized() == false {
		// HKWControlHandler has not been initialized before. Initialize it now.
		dispatch_async(dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_DEFAULT, 0), {
			if HKWControlHandler.sharedInstance().initializeHKWirelessController(g_licenseKey) != 0 {
				println("initializeHKWirelessControl failed")
				return
			}
				
			...
		})
	}


.. note:: 
	Note that ``intializeHKWirelessController()`` is a blocking call. So, it will not return until the caller initializes HKWireless Controller. If the phone is not connected to a Wi-Fi network, or any other app on the same phone is already using the HKWireless controller, then the call will wait until the app releases the controller. If you want non-blocking behavior, you should call this function asynchronously by running it in a separate thread, not within the main thread. So, it would be nice to present a dialog to user before calling ``initializeHKWirelessController()`` to notice that the app will wait until HKWirelessHD network is available. 

.. note:: 
	Note that even after a successful call to initializeHKWirelessController(), it takes a little time (a few hundreds milliseconds) to get the information of all the speakers available for streaming audio.


Getting the information of speakers available in the network
--------------------------------------------------------------

HKWControlHandler maintains the list of speakers available in the network. The list changes every time a speaker is added to the network or removed from the network. Whenever a speaker is added to or removed from the network, the **hkwDeviceStateUpdated()** delegate function is called. So developer needs to check which speaker has been added or removed, and handle the case accordingly, such as update the UI of speaker list, and so on.

Device Information
~~~~~~~~~~~~~~~~~~~

Each speaker information contains a list of attributes that specify its static information such as speaker name, group name, IP address, etc. and also dynamic information such as volume level, boolean value indicating if it is playing or not, Wi-Fi signal strength, etc. 

You can retrieve all the attributes of device (speaker) information through DeviceInfo object, and it is specified in DeviceInfo.h. The following table shows the list of information that DeviceInfo provides. 

In the table, "Fixed/Variable" column means if the attribute value is a fixed value during the execution, or can be changed by itself or by calling APIs. "Set by API" column means whether the value of the attribute can be changed by API calls. 

Note that all the attributes in DeviceInfo.h are "readonly". So, you can only read the value of each attribute. You need to use corresponding API functions to change the values of the attributes.


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


Getting a speaker (device) information
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

HKWControlHandler maintains the list of speaker internally. Each speaker information can be retrieved either by specifying the index in the table, or by specifying the index of group and the index of member inside of the group.

**Get the speaker information from the table**

You can retrieve a speaker information (as ``DeviceInfo`` object) by specifying the index in the table.

.. code-block:: swift

	- (DeviceInfo *) getDeviceInfoByIndex:(NSInteger)deviceIndex;

Here, the range of deviceIndex is 0 to the number of speakers (deviceCount) minus 1.

This function is useful when you need to show all the speakers in ordered list in ``TableViewCell``.

**Get a speaker information from the group list**

You can retrieve a speaker information by specifying a group index and the index of the speaker in the group.

.. code-block:: swift

	- (DeviceInfo *) getDeviceInfoFromTable:(NSInteger) groupIndex 
							deviceIndex:(NSInteger)deviceIndex;

Here, ``groupIndex`` represents the index of the group where the device belong to. ``deviceIndex`` means the index of the device in the group.

This function is useful to find the device information (``DeviceInfo`` object) that will be shown in a TableViewCell. For example, to show a speaker information in two section TableView, the ``groupIndex`` can correspond to the section number, and ``deviceIndex`` can correspond to the row number.







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


Change speaker name and group name
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

**Change speaker name and group name**

Use ``setDeviceName()`` to change the speaker name. Note that you cannot set the device name by setting “deviceName” property value directly. The property is read-only.

.. code-block:: swift
	HKWControlHandler.sharedInstance().setDeviceName(deviceId, deviceName:”My Omni10”)

**Change speaker’s group (room) name**

Use setDeviceGroupName() to change the group (or room) name of a speaker. Note that you cannot set the group name by setting “groupName” property value directly. The property is read-only.

.. code-block:: swift
	HKWControlHandler.sharedInstance().setDeviceGroupName(deviceId, groupName:”Living Room”)
	
.. note:: 
	If you change the group name of a speaker, then the list of devices of the groups automatically changes.

**Remove a speaker from a group (room)**

Use removeDeviceFromGroup() to remove the speaker from the currently belonging group. After being removed from a group, the name of group of the speaker is set to “harman”, which is a default group name implying that the speaker does not belong to any group.

.. code-block:: swift
	HKWControlHandler.sharedInstance().removeDeviceFromGroup(deviceId)

Add or remove a speaker to/from a playback session
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

To play a music on a specific speaker, the speaker should be added to the playback session.

Add a speaker to a session (to play on)
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^



[To be added]