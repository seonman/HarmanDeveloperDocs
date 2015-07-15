Programming Guide (iOS)
========================

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

----

Getting the information of speakers
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
	
	let deviceInfo: DeviceInfo = HKWControlHandler.sharedInstance().getDeviceInfoByGroupIndexAndDeviceIndex(groupIndex, deviceIndex:deviceIndex)
	
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

	- (DeviceInfo *) getDeviceInfoByGroupIndexAndDeviceIndex:(NSInteger) groupIndex 
							deviceIndex:(NSInteger)deviceIndex;

Here, ``groupIndex`` represents the index of the group where the device belong to. ``deviceIndex`` means the index of the device in the group.

This function is useful to find the device information (``DeviceInfo`` object) that will be shown in a TableViewCell. For example, to show a speaker information in two section TableView, the ``groupIndex`` can correspond to the section number, and ``deviceIndex`` can correspond to the row number.

**Get a speaker information with deviceId**

If you already knows the deviceId (device unique identifier) of a speaker, then you can retrieve the deviceInfo object with the following function.

.. code-block:: swift

	- (DeviceInfo *) getDeviceInfoById:(long long) deviceId;


Refreshing device status information
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

If any change happens on a speaker, the speaker sends an event with updated information to HKWControlHandler and then the speaker information stored in HKWControlHandler is updated. Then, HKWControlHandler calls corresponding delegate protocol functions registered by the app to make it processed by the event handler.

However, in our current implementation, the event dispatching initiated by speaker takes a little more time than the app polling to check if there is any update on speakers. To reduce the time of status update, we provide a pair of functions to refresh device status, which is a kind of polling speakers to check the update. Especially, if you need to show a list of speakers with the latest information, you'd better force to refresh the speaker information, not just waiting updates from speakers.

To discover and update the status of speakers immediately, you can use the following functions:

.. code-block:: swift

	// start to refresh devices ... 
	g_HKWControlHandler.startRefreshDeviceInfo()
	
	... ...
	
	// stop to refresh devices
	g_HKWControlHandler.stopRefreshDeviceInfo()  

``startRefreshDeviceInfo()`` will refresh and update every 2 seconds the status of the devices in the current Wi-Fi network.

Add or remove a speaker to/from a playback session
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

To play a music on a specific speaker, the speaker should be added to the current playback session.

You can check whether or not a speaker is currently added to the current playback session by check the "active" attribute of DeviceInfo object (in DeviceInfo.h).

.. code-block:: swift

	/*! Indicates if the speaker is active (added to the current playback session) */
	@property (nonatomic, readonly) BOOL active;


Add a speaker to a session (to play on)
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Use ``addDeviceToSession()`` to add a speaker to the current playback session.

.. code-block:: swift

	- (BOOL) addDeviceToSession:(long long) deviceid;

For example,

.. code-block:: swift

	// add the speaker to the current playback session
	g_HKWControlHandler.addDeviceToSession(deviceId)

If the execution is successful, then the attribute "active" of the speaker is set to "true".

.. note::
	A speaker can be added to the current on-going playback session anytime, even the playbach is started already. It usually takes a couple of seconds for the added speaker to start to play audio.

Remove a speaker from a session
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Use ``removeDeviceFromSession()`` to remove a speaker from current playback session. The removed speaker will stop playing audio immediately.

.. code-block:: swift

	- (BOOL) removeDeviceFromSession:(long long) deviceid;

For example,

.. code-block:: swift

	// remove a speaker from the current playback session
	g_HKWControlHandler.removeDeviceFromSession(deviceId)
	
If the execution is successful, then the attribute "active" of the speaker is set to "false".

.. note::
	A speaker can be removed from the current on-going playback session anytime.

.. note::
	After a speaker was removed from the session and there is no speaker remaining in the session, then the current playback stops automatically.

----

Play Audio File
----------------

Play CAF file (MP3, WAV, etc.)
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

If one or more speakers are available to the session, or if there is at least one speaker active, you can start to play a song. 

The playback is based on the Apple Core Audio framework. So, the supported audio file and data formats by HKWirelessHDSDK are the same as those supported by Apple's Core Audio framework. The detailed information is available at `iOS audio file formats`_. According to the Apple developer documentation, CAF supports AIFF (.aif, .aiff), CAF (.caf), MPEG-1, Layer 3 (.mp3), MPEG-2 or MPEG-4 ADTS (.aac), MPEG-4 Audio (.mp4, .m4a), and WAVE (.wav).
 
.. _iOS audio file formats: https://developer.apple.com/library/ios/documentation/MusicAudio/Conceptual/CoreAudioOverview/CoreAudioEssentials/CoreAudioEssentials.html#//apple_ref/doc/uid/TP40003577-CH10-SW57
 

.. code-block:: swift

	- (BOOL) playCAF:(NSURL *)assetURL songName:(NSString*)songName resumeFlag:(BOOL)resumeFlag;

To play a song, you should prepare a ``AssetURL`` using ``NSURL`` first. Here is an example:

.. code-block:: swift

	let assetUrl = NSURL(fileURKWithPath: nsPath)
	
	HKWControlHandler.sharedInstance().playCAF(assetUrl, songName: songTitle, resumeFlag: false)

Here, ``resumeFlag`` is false, if you start the song from the beginning. If you want to resume to play the current song, then ``resumeFlag`` should be true. ``songTitle`` is a string, representing the song name. (This is only internally used as a file name to store converted PCM data in the memory temporarily.)

If you want to specify a starting point of the audio stream, then you can use ``playCAFFromCertainTime()`` to start the playback from a specified time.

.. code-block:: swift

	- (bool) playCAFFromCertainTime:(NSURL *)assetURL
							songName:(NSString*)songName
							startTime:(NSInteger)startTime;

Here, ``startTime`` is in second.

``playCAF()`` and ``playCAFFromCertainTime()`` can play both WAV and MP3 audio file. In case of WAV, it is played without conversion. In case of MP3, it is converted to PCM format first, and then played.

To play WAF audio file, use ``playWAV()``.

.. code-block:: swift

	- (bool) playWAV:(NSString*)wavPath;

The following example shows how to play a WAV file stored in the application bundle.

.. code-block:: swift

	nsWavPath = NSBundle.mainBundle().bundlePath.stringByAppendingPathComponent(songTitle)
	
	HKWControlHandler.sharedInstance().playWAV(nsWavPath)

.. note::
	``playCAF()`` and ``playCAFFromCertainTime()`` cannot play an audio file in Apple’s iTunes Match service. Songs should reside locally on the device for playback. So, it would be nice to check if the song resides on the device when your app gets the MPMediaItem of a song from MediaPicker.

You can check the playback status anytime, that is, before and after as well as in the middle of the playback. You can get the player status by calling ``getPlayerState()``. (``HKPlayerState`` is defined in HKWControlHandler.h)

.. code-block:: swift

	- (HKPlayerState)getPlayerState;

If you just want to check if the player is playing audio now, then use isPlaying().

.. code-block:: swift

	- (bool) isPlaying;
	
Play Web Streaming Music
~~~~~~~~~~~~~~~~~~~~~~~~~~

.. note::
	This API is **NOT** supported by HKWirelessHDSDKlw (lightweight version of HKWirelessHDSDK). It is only supported by HKWirelessHD SDK.
	
Use ``playStreamingMedia()`` to playt a streaming media. It uses a parameter of ``streamingMediaUrl`` to specify the URL of the media file in the streaming service. It starts with a protocol name, such as "http://" or "rtps://". Currently, http:, rtps:, and mms: are supported. The supported file format is mp3, m4a, wav.


``completedCallback`` is a callback that returns the result of the call, that is, if the call is successful or not. If it cannot find the media file on the server or some other errors occur, then it return false.

.. code-block:: swift

	- (void)playStreamingMedia:(NSString *)streamingMediaUrl withCallback:(void (^)(bool result))completedCallback;

.. note::
	When you stop playing the streaming music, you must use stop(), not pause().
	
	
Playback controls
^^^^^^^^^^^^^^^^^^

**Stop playback**

To stop the current playback, use ``stop()``. As a result. the playback status is changed to ``EPlayerState_Stop``, and ``hkwPlaybackStateChanged()`` delegate protocol is called if implemented. 

.. code-block:: swift

	- (void) stop;

For example,

.. code-block:: swift

	HKWControlHandler.sharedInstance().stop()

It is safe to call ``stop()`` even if there is no on-going playback. Actually, we recommand to call ``stop()`` before you start to play a new audio stream.

.. note::
	If the current playback is stopped by calling ``stop()``, the playback cannot be resumed. Resuming is only possible when the playback is paused by calling ``pause()``.
	
**Pause playback**

To pause the current play, use ``pause()``. As a result, the playback status is changed to ``EPlayerState_Pause``, and ``hkwPlaybackStateChanged()`` delegate protocol is called if implemented.

The playback paused by calling ``pause()`` can be resumed.

.. code-block:: swift

	- (void) pause;

For example,

.. code-block:: swift

	HKWControlHandler.sharedInstance().pause()

**Resume playback**

To resume the paused playback, use ``playCAF()`` with the parameter ``resumeFlag`` set to ``true``. The API is described earlier in this section. 

Volume Control
~~~~~~~~~~~~~~~

You can set volumes in two ways – one is set volume for an individual speaker, and the other is set volume for all speakers with the same volume level. The volume level ranges from 0 (mute) to 50 (max).

.. note::
	Volume change funcions are all asynchronous call. That is, it takes a little time (a few milli second) for a volue change to take effect on the speakers.

.. note::
	When ``setVolumeDevice()`` is called, the average volume can be also changed. So, it is safe to retrieve the speaker volumes using VolumeLevelChanged callback (explained later) when your app calls volume control APIs.

Set volume to all speakers
^^^^^^^^^^^^^^^^^^^^^^^^^^^

Use ``setVolume()`` to set the same volume level to all speakers.

.. code-block:: swift

	- (void) setVolume:(NSInteger)volume;

For example,

.. code-block:: swift

	// set volume level to 25 to all speakers
	var volume  = 25
	HKWControlHandler.sharedInstance().setVolume(volume)

Set volume to a particular speaker 
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Use ``setVolumeDevice()`` to set volume to a particular speaker. You need to specify the deviceId for the speaker.

.. code-block:: swift

	- (void) setVolumeDevice:(long long)deviceId volume:(NSInteger)volume;

For example,

.. code-block:: swift

	// set volume level to 25 to a speaker
	var volume  = 25
	HKWControlHandler.sharedInstance().setVolumeDevice(deviceId volume:volume)

Get volume of all speakers
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Use ``getVolume()`` to get the average volume level fro all speakers.

.. code-block:: swift

	- (NSInteger) getVolume;

For example,

.. code-block:: swift

	var averageVolume = HKWControlHandler.sharedInstance().getVolume()

Get volume of a particular speaker
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Use ``getDeviceVolume()`` to get the volume level of a particular speaker. 

For example,

	var volume = HKWControlHandler.sharedInstance().getDeviceVolume(deviceId)


Mute and Unmute
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Use ``mute()`` to mute the current playback. The volume level turns to 0. 

Use ``unmute()`` to unmute the current muted playback. After ``unmute()``, the volume level returns to the original volume level before ``mute()`` is called.

Use ``isMuted()`` to check if the current playback is muted or not.


----

Speakers and Groups
---------------------

In HKWirelessHD SDK, a group is a collection of speakers. A group is defined as below:

- The group of a speaker is defined by specifying a group name in the speaker information as attribute.
- A speaker can join only one group at a time. The meaning of "joining a group" is to have the group name in its attribute.
- All the speakers with the same group name belong to the same group associated with the group name.
- The group ID is determined by following the device ID of the initial member of a group. For example, there is no group with the name "Group-A", and Speaker-A sets the group as "Group-A", then the GroupID is created with the deviceID of Speaker-A. After that, if Speaker-B joins Group-A, then the group name and the group ID are set by the ones that Speaker-A has.

Change speaker name
~~~~~~~~~~~~~~~~~~~~~

Use ``setDeviceName()`` to change the speaker name. 

.. note::
	You cannot set the device name by setting ``deviceName`` property value directly. The property is read-only.

.. code-block:: swift

	- (void) setDeviceName:(long long)deviceId deviceName:(NSString *)deviceName;

For example,

.. code-block:: swift

	HKWControlHandler.sharedInstance().setDeviceName(deviceId, deviceName:”My Omni10”)

.. note::
	While a speaker is playing audio, if the name of the speaker is changed, then the current playback is interrupted (stopped) with error. The error code and message are returned by ``hkwErrorOccurred()`` delegate defined in ``HKWDeviceEventHandlerDelegate`` protocol.


Set the group for a speaker
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

To set a group for a speaker (in other words, to join a speaker to a group), use ``setDeviceGroupName()`` as below:

.. code-block:: swift

	- (void) setDeviceGroupName:(long long)deviceId groupName:(NSString *)groupName;

For example,

.. code-block:: swift

	HKWControlHandler.sharedInstance().setDeviceGroupName(deviceId, groupName:”Living Room”)

.. note::
	If you change the group name of a speaker, then the list of speakers of the group automatically changes.

Remove a speaker from a group
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Use ``removeDeviceFromGroup()`` to remove the speaker from the currently belonged group. After being removed from a group, the name of group of the speaker is set to ``harman``, which is a default group name implying that the speaker does not belong to any group.

.. code-block:: swift

	- (void)removeDeviceFromGroup:(long long)deviceId;

For example,

.. code-block:: swift

	HKWControlHandler.sharedInstance().removeDeviceFromGroup(deviceId)

----

Delegate APIs for events handling
----------------------------------

In HKWirelessHD, the communication between user’s phone and speakers are done in asynchronous way. Therefore, some API calls from HKWControlHandler can take a little time to take effects on the speaker side. Similarly, any change of status on the speaker side are reported to the phone a little time later. For example, the status of availability of a speaker can be updated a few seconds later after a speaker turns on or off. 

All the status update from the speaker side are reported to the phone via **delegate protocols**. So, your app needs to implement the delegate protocols accordingly to receive and handle the events from HKWControlHandler.

There are two kinds of delegate protocols for event handling.

- HKWDeviceEventHandlerDelegate (defined in HKWDeviceEventHandlerSingleton.h)
	- This delegate protocol defines several APIs for receiving events about status changes or error from speakers.
- HKWPlayerEventHandlerDelegate (defined in HKWPlayerEventHandlerSingleton.h)
	- This delegate protocol defines several APIs for receiving events about playback status and volume control.

HKWDeviceEventHandlerDelegate
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

**hkwDeviceStateUpdated (required)**

This function is invoked when some of device information have been changed on a particular speaker. The information being monitored incudes device status (active or inactive), model name, group name, and wifi signal strengths, etc. The parameter ``reason`` specifies what the update is about. The reason code is defined in HKWControlHandler.h.

Note that volume level change does not trigger this call. The volume update is reported by ``hkwVolumeLevelChanged()`` callback.

.. code-block:: swift

	-(void) hkwDeviceStateUpdated:(long long)deviceId withReason:(NSInteger) reason;

This callback is essential to retrieve and update the speaker information in timely manner. If your app has a screen that shows a list of speakers available in the network with latest information, you can receive the event via this function and update the list.

A most common usage for this function is to invole ``tableView.reloadData()`` to update the list of speakers in a table view controller, as shown below.

.. code-block:: swift

	func hkwDeviceStateUpdated(deviceId: CLongLong, withReason reason:Int) {
		self.tableView.reloadData()
	}

**hkwErrorOccured (required)**

This function is invoked when an error occurs during the execution. The callback returns the error code, and also corresponding error message for detailed description. The error codes are defined in HKWCOntrolHandler.h.

.. code-block:: swift

	-(void) hkwErrorOccurred:(NSInteger)errorCode withErrorMessage:(NSString*)errorMesg;

A most common usage of this function is to show an alert dialog to notice the user of the error.

HKWPlayerEventHandlerDelegate
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

**hkwPlayEnded (required)**

This function is invoked when the current playback has ended.

.. code-block:: swift

	-(void)hkwPlayEnded;

This function is useful to take any actioin when the current playback has ended.

**hkwDeviceVolumeChanged (optional)**

This function is invoked when volume level is changed for any speakers. It is called asynchronously right after any of SetVolume APIs are called by apps.

The function delivers the device ID of the speaker with volume changed, a new device volume level, and average volume level value, as below:

.. code-block:: swift

	-(void)hkwDeviceVolumeChanged:(long long)deviceId deviceVolume:(NSInteger)deviceVolume withAverageVolume:(NSInteger)avgVolume;

.. note::
	When speaker volume is changed by a call to ``setVolume()``, then all the speakers are set to a new volume level, and this function can be used to get the new volume level value.
	
**hkwPlaybackStateChanged (optional)**
This function is invoked when playback state is changed during the playback. The callback delivers the playState value as parameter.

.. code-block:: swift

	-(void)hkwPlaybackStateChanged:(NSInteger)playState;

**hkwPlaybackTimeChanged (optional)**

This function is invoked when the current playback time is changed. It is called every one second. The function parameter timeElapsed returns the time (in second) elapsed since the start of the playback. This function is useful when your app update the progress bar of the current playback.

.. code-block:: swift

	-(void)hkwPlaybackTimeChanged:(NSInteger)timeElapsed;

How to implement the delegate protocols
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

To make your ViewController as delegate, that is, to receive the events from HKWControlHandler in your ViewController, your ViewController class has to implement the protocol by specifying the delegate protocol name in the class definition as below:

.. code-block:: swift

	class SpeakerListViewController: UIViewController, UITableViewDataSource,   
		SpeakerTableCellDelegate, HKWDeviceEventHandlerDelegate  {
		... ...
	}
	
And then, the ViewController class should set itself to ``delegate`` attribute of the handler singleton class as below:

.. code-block:: swift

	HKDeviceEventHandlerSingleton.sharedInstance().delegate = self


Note that during the runtime, only one instance of the event handler for HKWControlHandler is instantiated, so it is designed as a singleton. It can be retrieved by calling ``sharedInstance()`` to the class.

**For device event handler**

.. code-block:: swift

	HKWDeviceEventHandlerSingleton.sharedInstance().delegate

**For player event handler**

.. code-block:: swift

	HKWPlayerEventHandlerSingleton.sharedInstance().delegate

.. note::

	Since there is only one instance of delegator for each delegate, if you set delegate in several different places of your app, then the latest setting will override the delegate value, and the previous setting will be overridden. So, you need to set the delegate every time you show a ViewController, and one right place to set the delegate is inside ``viewDidAppear()`` in ViewController class as shown below.
	
.. code-block:: swift
	
	override func viewDidAppear(animated: Bool) {
		HKWDeviceEventHandlerSingleton.sharedInstance().delegate = self
		HKWPlayerEventHandlerSingleton.sharedInstance().delegate = self
		
		...        
		
	}
	
	
