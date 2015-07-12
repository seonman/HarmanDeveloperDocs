.. _smartapp_ref:

API Documentation
===================


Initialization
------------------

Refer to HKWControlHandler.h in the SDK.


initializeHKWirelessController()
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Initializes and starts HKWirelessHD controller. This API requires a license key as string. If the input license key fails in key validation, then the API returns -1. If it is successful, return 0.

The license key will be delivered to you once you sign on to developer.harman.com. Until it is delivered, you may use the license key included in the sample apps.

.. note::
	This is a blocking call, and it will not return until the caller successfully initializes HKWireless controller. If the phone is not connected to a Wi-Fi network, or any other app on the same phone is using the HKWireless controller, it waits until the other app releases the controller.

	If you needs non-blocking behavior, you should call this API asynchronously using other thread.


**Signature:**
    ``- (NSInteger) initializeHKWirelessController:(NSString*)licenseKey;``

**Parameters:**

- ``(NSString*) licenseKey`` - a string containing the license key
	
**Returns:**
    ``NSInteger`` - an integer indicating success (0 or HKW_INIT_SUCCESS) or failure (-1 or HKW_INIT_FAILURE_LICENSE_INVALID)

----

isInitialized()
~~~~~~~~~~~~~~~~~~

Checks if HKWirelessHD controller is already initialied.

**Signature:**
	``- (BOOL) isInitialized;``
	
**Returns:**
	``BOOL`` - boolean value indicating if HKWirelessController has been initialized or not.

----

initializing()
~~~~~~~~~~~~~~~~

Checks if HKWirelessHD controller is being initialied.

**Signature:**
	``- (BOOL) initializing;``
	
**Returns:**
	``BOOL`` - boolean value indicating if HKWirelessController is being initialized

Refreshing Speaker Information
-------------------------------

refreshDeviceInfoOnce()
~~~~~~~~~~~~~~~~~~~~~~~~

Refreshes the device status one time. The device information will be refreshed and updated to ``DeviceInfo`` objects (defined in DeviceInfo.h). If there is any update on ``DeviceInfo``, then ``hkwDeviceStateUpdated`` delegate function defined by ``HKWDeviceEventHandlerDelegate`` will be called. From there, you can see which device has been updated and what was the reason of the update.

**Signature:**
	``- (void) refreshDeviceInfoOnce;``

**Returns:**
	``void``
	
----

startRefreshDeviceInfo()
~~~~~~~~~~~~~~~~~~~~~~~~

Starts to keep refreshing ``DeviceInfo`` every two seconds. It continues until ``stopRefreshDeviceInfo()`` is called.

**Signature:**
	``- (void) startRefreshDeviceInfo;``
	
**Returns:**
	``void``
	
----

stopRefreshDeviceInfo()
~~~~~~~~~~~~~~~~~~~~~~~~~

Stops refreshing ``DeviceInfo``, which was initiated by ``startRefreshDeviceInfo()``.

**Signature:**
	``- (void) stopRefreshDeviceInfo;``
	
**Returns:**
	``void``

----

Playback Control
------------------

playCAF()
~~~~~~~~~

Plays a CAF audio file in local storage. If it is successful, ``hkwPlaybackStateChanged()`` delegate function will called and return the status value of ``HKPlayerState.EPlayerState_Play``.

The playback uses Apple Core Audio framework. So, the types of supported audio files and data formats by HKWirelessHDSDK are the same as those supported by Apple's Core Audio framework. The detailed information is available at `iOS audio file formats`_. According to the Apple developer documentation, CAF supports AIFF (.aif, .aiff), CAF (.caf), MPEG-1, Layer 3 (.mp3), MPEG-2 or MPEG-4 ADTS (.aac), MPEG-4 Audio (.mp4, .m4a), and WAVE (.wav).

.. _iOS audio file formats: https://developer.apple.com/library/ios/documentation/MusicAudio/Conceptual/CoreAudioOverview/CoreAudioEssentials/CoreAudioEssentials.html#//apple_ref/doc/uid/TP40003577-CH10-SW57

**Signature:**
	``- (BOOL) playCAF:(NSURL *)assetURL songName:(NSString*)songName resumeFlag:(BOOL)resumeFlag;``

**Parameters:**

- ``(NSURL*) assetURL`` - NSURL to the audio file
- ``(NSString*) songName`` -  the song name to be played. The songName is used internally to save the temporary PCM file generated from the original audio file.
- ``(BOOL) resumeFlag`` -  a boolean specifying if the playback should resume from the point where it was paused in the previous playback. When you want to start a song from the beginning, resumeFlag must be false. If the previous playback was stopped not paused, then the playback will start from the beginning even with resumeFlag true.

**Returns:**
	``BOOL`` - boolean value indicating success or failure

----

playCAFFromCertainTime()
~~~~~~~~~~~~~~~~~~~~~~~~~~

Plays a CAF audio file beginning from a certain time of playback specified by ``startTime``. For example, you can start a song from the point of 10 seconds of the song. ``hwkPlaybackStateChanged()`` delegate function will be called and return the status value of ``HKPlayerState.EPlayerState_Play``.

**Signature:**
	``- (BOOL) playCAFFromCertainTime:(NSURL *)assetURL songName:(NSString*)songName startTime:(NSInteger)startTime;``

**Parameters:**

- ``(NSURL *)assetURL`` - NSURL to the audio file.
- ``(NSString*)songName`` - the song name to be played. The songName is used internally to save the temporary PCM file generated from the original audio file.
- ``(NSInteger)startTime`` - time in second that specifies the start time.

**Returns:**
	``BOOL`` - boolean value indicating success or failure

----

playWAV()
~~~~~~~~~~~~

Plays a WAV file. ``hkwPlaybackStateChanged`` delegate function will be called and return the status value of ``HKPlayerState.EPlayerState_Play``.

**Signature:**
	``- (BOOL) playWAV:(NSString*)wavPath;``

**Returns:**
	``BOOL`` - boolean value indicating success or failure
	
playStreamingMedia()
~~~~~~~~~~~~~~~~~~~~~~

Plays a streaming media from web server. Because this API takes a little while to get the result of play because of all networking stuffs, the API just returns immediately, and instead it takes a callback to be called when the result is ready. You should take the result of the call inside of the callback.

**Signature:**
	``- (void)playStreamingMedia:(NSString *)streamingMediaUrl withCallback:(void (^)(bool result))completedCallback;``

**Parameters:**

- ``(NSString*)streamingMediaUrl`` - a string that specifies the URL of the streaming media source. It starts with a protocol name, such as "http://" or "rtps://". Currently, http, rtps, and mms are supported. The supported file format is mp3, m4a, and wav.
- ``(void (^)(bool result))completedCallback`` - a callback that returns the result of the playback

**Returns:**
	``void``
	
----

pause()
~~~~~~~~~~

Pauses the current playback. ``hkwPlaybackStateChanged()`` delegate function will be called and return the status value of ``HKPlayerState.EPlayerState_Pause``. Once the playback is paused, it can resume by calling ``playCAF()`` with ``resumeFlag`` true and the same ``songName``. A playback by ``playStreamingMedia()`` cannot be paused. If ``pause()`` is called, then the playback will stop.

**Signature:**
	``- (void) pause;``

**Returns:**
	``void``

----

stop()
~~~~~~~~~

Stops the current playback. ``hkwPlaybackStateChanged()`` delegate function will be called and return the status value of ``HKPlayerState.EPlayerState_Stop``.

**Signature:**
	``- (void) stop;``

**Returns:**
	``void``

----

isPlaying()
~~~~~~~~~~~~

Checks if the player is playing some audio or not.

**Signature:**
	``- (bool) isPlaying;``

**Returns:**
	``BOOL`` - boolean value indicating if the player is playing or now.

----
	
getPlayerState()
~~~~~~~~~~~~~~~~~~~

Gets the current state of playback.

**Signature:**
	``- (HKPlayerState)getPlayerState;``
	
**Returns:**
	``HKPlayState`` - indicates the current player state. 
	
----

Volume Control
----------------

setVolume()
~~~~~~~~~~~~~~~~

Sets a volume level to all speakers. The same volume level is set to all speakers.

The range of volume level is 0 to the maximumVolumeLevel (currently, 50) defined by ``getMaximumVolumeLevel()``.

Setting volume is asynchronous call. So, the effect of the API call will occur after a few milliseconds. The ``hkwDeviceVolumeChanged()`` delegate function defined in ``HKWPlayerEventHandlerDelegate`` (in HKWPlayerEventHandlerSingleton.h) will be called when the volume level of the specified speaker has actually changed.

If the volume is being muted by calling ``mute()``, the volume level is changed from the original volume level before mute.

**Signature:**
	``- (void) setVolume:(NSInteger)volume;``

**Parameters:**

- ``(NSInteger)volume`` -  the volume level to set

**Returns:**
	``void``

----

setVolumeDevice()
~~~~~~~~~~~~~~~~~~~~

Set a volume level to a speaker specified by device ID. The range of volume level is 0 to the maximumVolumeLevel (currently, 50) defined by ``getMaximumVolumeLevel()``. ``setVolume`` is asynchronous call. So, the effect of the API call will occur after a few milliseconds. The ``hkwDeviceVolumeChanged()`` delegate function defined in ``HKWPlayerEventHandlerDelegate`` (in HKWPlayerEventHandlerSingleton.h) will be called when the volume level of the specified speaker has actually changed.

If the volume is being muted by calling ``mute()``, the volume level is changed from the original volume level before mute.

**Signature:**
	``- (void) setVolumeDevice:(long long)deviceId volume:(NSInteger)volume;``

**Parameters:**

- ``(long long)deviceId`` - the device ID of the speaker
- ``(NSInteger)volume`` -  the volume level to set

**Returns:**
	``void``
	
----

getVolume()
~~~~~~~~~~~~~

Gets the average volume level for all devices.

**Signature:**
	``- (NSInteger) getVolume;``
	
**Returns:**
	``NSInteger`` - the average volume level of all speakers

----

getDeviceVolume()
~~~~~~~~~~~~~~~~~~~

Gets the volume level of the specified speaker.

**Signature:**
	``- (NSInteger) getDeviceVolume:(long long)deviceId;``

**Parameters:**
- ``(long long)deviceId`` - the deviceId of the speaker inquired.

**Returns:**
	``NSInteger`` - the device volume level
	
----

getMaximumVolumeLevel()
~~~~~~~~~~~~~~~~~~~~~~~~~

Returns the maximum volume level that the system provides. Currently, it is 50.

**Signature:**
	``- (NSInteger) getMaximumVolumeLevel;``

**Returns:**
	``NSInteger`` - the maximum volume level

mute()
~~~~~~~~

Mutes the current volume of all speakers.

**Signature:**
	``- (void) mute;``
	
**Returns:**
	``void``
	
----

unmute()
~~~~~~~~~~

Unmute the volume. It returns the previous volume level before mute.

**Signature:**
	``- (void) unmute;``
	
**Returns:**
	``void``

----

isMuted()
~~~~~~~~~~~

Check if volume is muted or not.

**Signature:**
	``- (bool) isMuted;``
	
**Returns:**
	``BOOL``  - the Boolean value indicating if mute is on or not.

----

Device (Speaker) Management
------------------------------

addDeviceToSession()
~~~~~~~~~~~~~~~~~~~~~~~

Adds a speaker to the current playback session. The added speaker will start playing audio. This can be done during the audio playback.

**Signature:**
	``- (BOOL) addDeviceToSession:(long long) deviceid;``

**Parameters:**

- ``(long long)deviceId`` - The ID of the device to add

**Returns:**
	``BOOL`` - boolean value indicating whether the addition is successful or not.

----

removeDeviceFromSession()
~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Removes a speaker from the current playback session. The removed speaker will stop playing audio. This can be done during the audio playback.

**Signature:**
	``- (BOOL) removeDeviceFromSession:(long long) deviceid;``

**Parameters:**

- ``(long long)deviceId`` -  The ID of the device to remove

**Returns:**
	``BOOL`` - boolean value indicating whether the removal is successful or not.

----

getDeviceCount()
~~~~~~~~~~~~~~~~~~

Gets the number of all speakers in the HKWirelessHD network.

**Signature:**
	``- (NSInteger) getDeviceCount;``

**Returns:**
	``NSInteger`` - the number of speakers.

----

getGroupCount()
~~~~~~~~~~~~~~~~~

Gets the number of the groups of speakers.

**Signature:**
	``- (NSInteger) getGroupCount;``

**Returns:**
	``NSInteger`` - the number of the groups

----
 
getDeviceCountInGroupIndex()
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Gets the number of the speakers that belongs to a group specified by the index.

**Signature:**
	``- (NSInteger) getDeviceCountInGroupIndex:(NSInteger)groupIndex;``

**Parameters:**

- ``(NSInteger)groupIndex`` - the index of the group looking for. It starts from 0 to (GroupCount-1).

**Returns:**
	``NSInteger`` - the number of speakers

----

getDeviceInfoByGroupIndexAndDeviceIndex()
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Returns the ``DeviceInfo`` object (defined in DeviceInfo.h) pointed by groupIndex and deviceIndex. This API is useful to find a ``DeviceInfo`` that will be shown in a TableViewCell. For example, to show a speaker information in two section TableView, the groupIndex can correspond to the section number, and deviceIndex can correspond to the row number.

**Signature:**
	``- (DeviceInfo *) getDeviceInfoByGroupIndexAndDeviceIndex:(NSInteger) groupIndex deviceIndex:(NSInteger)deviceIndex;``

**Parameters:**

- ``(NSInteger)groupIndex`` - The index of the group where the speaker belongs to.
- ``(NSInteger)deviceIndex`` -  The index of the speaker in the group.

**Returns:**
	``DeviceInfo*`` - the DeviceInfo object
 
----
 
getDeviceInfoByIndex()
~~~~~~~~~~~~~~~~~~~~~~~~~

Returns the ``DeviceInfo`` object pointed by deviceIndex from the table containing all speakers. The range of deviceIndex will be 0 to (deviceCount - 1).

**Signature:**
	``- (DeviceInfo *) getDeviceInfoByIndex:(NSInteger)deviceIndex;``
	
**Parameters:**
- ``(NSInteger)deviceIndex`` -  The index of the device from the table with all devices.

**Returns:**
	``DeviceInfo*`` - the DeviceInfo object
	
----

getDeviceGroupByDeviceId()
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Returns the object of the ``DeviceGroup`` that a speaker belongs to.

**Signature:**
	``- (DeviceGroup *)getDeviceGroupByDeviceId:(long long)deviceId;``

**Parameters:**

-- ``(long long)`` -  the ID of the speaker that belongs to a DeviceGroup

**Returns:**
	 ``DeviceGroup*`` - the DeviceGroup object

----

getDeviceInfoById()
~~~~~~~~~~~~~~~~~~~~~~~

Finds a ``DeviceInfo`` from the table by device Id. It is useful to retrieve ``DeviceInfo`` with a particular deviceId.

**Signature:**
	``- (DeviceInfo *) getDeviceInfoById:(long long) deviceId;``

**Parameters:**

- ``(long long)deviceId`` - the ID of the device we are looking for.

**Returns:**
	``DeviceInfo*`` - The DeviceInfo object

----

isDeviceAvailable()
~~~~~~~~~~~~~~~~~~~

Checks whether the specified speaker is available on the network or not.

**Signature:**
	``- (BOOL) isDeviceAvailable:(long long)deviceId;``
	
**Parameters:**
- ``(long long)deviceId`` - The ID of the speaker

**Returns:**
	``(BOOL)`` - boolean indicating if the speaker is available or not.

----

isDeviceActive()
~~~~~~~~~~~~~~~~~~~

Checks whether the speaker is active (added to the current playback session) or not.

**Signature:**
	``- (BOOL) isDeviceActive:(long long)deviceId;``
	
**Parameters:**
- ``(long long)deviceId`` - The ID of the speaker

**Returns:**
	``(BOOL)`` - boolean indicating if the speaker is active or not.

----

removeDeviceFromGroup()
~~~~~~~~~~~~~~~~~~~~~~~~~~~

Removes (ungroup) the specified speaker from the currently belonged group. It is done internally by setting the GroupName as "harman" (which is factory default device name, and implies Not-Assigned.).

**Signature:**
	``- (void)removeDeviceFromGroup:(long long)deviceId;``

**Parameters:**
- ``(long long)deviceId`` - The ID of the speaker to ungroup.

**Returns:**
	``void``
	
----

getDeviceGroupByIndex()
~~~~~~~~~~~~~~~~~~~~~~~~~

Gets the ``DeviceGroup`` object by index.

**Signature:**
	``- (DeviceGroup *)getDeviceGroupByIndex:(NSInteger)groupIndex;``

**Parameters:**

- ``(NSInteger)groupIndex`` - the index of the group

**Returns:**
	``DeviceGroup*`` - The object of DeviceGroup
 
----

getDeviceGroupByGroupId()
~~~~~~~~~~~~~~~~~~~~~~~~~~~

Gets DeviceGroup by group ID.

**Signature:**
	``- (DeviceGroup *)getDeviceGroupByGroupId:(long long)groupId;``

**Parameters:**
	- ``(long long)groupId`` - the ID of the group

**Returns:**
	``DeviceGroup*`` - the object of device group.
 
----

getDeviceGroupNameByIndex()
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Gets the name of the DeviceGroup by index.

**Signature:**
	``- (NSString *)getDeviceGroupNameByIndex:(NSInteger)groupIndex;``

**Parameters:**

- ``(NSInteger)groupIndex`` - the index of the group in the group table.

**Returns:**
	``NSString*`` - the string of group name

----


getDeviceGroupIdByIndex()
~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Gets the ID of the DeviceGroup by index.

**Signature:**
	``- (long long)getDeviceGroupIdByIndex:(NSInteger)groupIndex;``

**Parameters:**

- ``(NSInteger)groupIndex`` - the index of the group in the table

**Returns:**
	``long long`` - the group id
 
----

setDeviceName()
~~~~~~~~~~~~~~~~~~

Sets device name to a speaker. Note that you cannot set the device name by setting "deviceName" property directly. The property is read-only.

.. note::

	If you set a device name while the a playback is running, the speaker stops playing and return error.

**Signature:**
	``- (void) setDeviceName:(long long)deviceId deviceName:(NSString *)deviceName;``

**Parameters:**

- ``(NSInteger)deviceId`` - The ID of the device
- ``(NSString*)deviceName`` - The name of the device to set

**Returns:**
	``void``

----

setDeviceGroupName()
~~~~~~~~~~~~~~~~~~~~~~

Sets device group to a speaker with Group name. Note that you cannot set the group name by setting "groupName" property directly. The property is read-only.

.. note::

	If you set a group name while the a playback is running, the speaker stops playing and return error.

**Signature:**
	``- (void) setDeviceGroupName:(long long)deviceId groupName:(NSString *)groupName;``
	
**Parameters:**

- ``(NSInteger)deviceId`` - The ID of the device
- ``(NSString*)groupName`` - The name of the group name to set

**Returns:**
	``void``

----

setDeviceRole()
~~~~~~~~~~~~~~~~~

Sets the role for the speaker. The role information is used to define which part of audio channel the speaker takes for the playback.

**Signature:**
	``- (void)setDeviceRole:(long long)deviceId role:(int)role;``

**Parameters:**

- ``(long long)deviceId`` - The id of the device
- ``(int)role`` - the interger value indicating the role of the speaker. The possible options are listed in the HKRole enumeration type. The default value is EMono (21).
 
**Returns:**
	``void``

----

getActiveDeviceCount()
~~~~~~~~~~~~~~~~~~~~~~~~

 Gets the number of active speakers (the speakers that are being added to the current playback session.)

**Signature:**
	``- (NSInteger) getActiveDeviceCount;``

**Returns:**
	``NSInteger`` - the number of active devices
 
----

getActiveGroupCount()
~~~~~~~~~~~~~~~~~~~~~~~

Gets the number of active groups. An active group is defined as a group that all the speakers that belongs to a group are active. If even one of the speakers in the same group is inactive, then the group is inactive.
 
**Signature:**
	``- (NSInteger) getActiveGroupCount;``
 
**Returns:**
	``NSInteger`` - the number of active groups

----

refreshDeviceWiFiSignal()
~~~~~~~~~~~~~~~~~~~~~~~~~~~

Refreshes the device's Wifi Signal strength value. This is asynchronous call, and the result of refreshing will come a few milliseconds later. The new WiFi signal strength value will be reported by ``hkwDeviceStateUpdated()`` delegate function defined in HKWDeviceEventHandlerDelegate (in HKWDeviceEventHandlerSingleton.h). In ``hkwDeviceStateUpdated()``, you should get the wifi signal value by getting ``wifiSignalStrength`` attribute of ``DeviceInfo`` object.

**Signature:**
 	``- (void)refreshDeviceWiFiSignal:(long long)deviceId;``
 
**Parameters:**

- ``(long long)deviceId`` - The ID of the device

**Returns:**
	``void``
	
----
 

getWifiSignalStrengthType()
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Gets Wifi signal strength type by signal value.

**Signature:**
	``- (HKWifiSingalStrength)getWifiSignalStrengthType:(NSInteger)wifiSignal;``

**Parameters:**

- ``(NSInteger)wifiSignal`` - the wifi signal value

**Returns:**
	``HKWifiSingalStrength`` - a value of HKWifiSignalStrength type

----
	
HKWDeviceEventHandlerSingleton
-------------------------------

sharedInstance()
~~~~~~~~~~~~~~~~~~~~

Returns the singleton object to HKWDeviceEventHandler. 

**Signature:**
+(HKWDeviceEventHandlerSingleton*)sharedInstance;

----

delegate
~~~~~~~~~~~~~

The delegate attribute to be set by an object to be a HKWDeviceEventHandlerDelegate.

.. code-block:: objective-c

	@property (nonatomic, weak) id<HKWDeviceEventHandlerDelegate> delegate;

----

HKWDeviceEventHandlerDelegate
-------------------------------

hkwDeviceStateUpdated() - required
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Invoked when some of device information have been changed for any speakers.It is also invoked when the network is disconnected ans no speakers are available any longer, or when the network becomes up from down, so speakers in the network become added to the HKWirelessHD network. <p>The information monitored includes device status (active or inactive), model name, group name, and wifi signal strengths.<p>Volume change does not trigger this call. The volume update is reported by CallbackVolumeLevelChanged.

**Signature:**
	``-(void)hkwDeviceStateUpdated:(long long)deviceId withReason:(NSInteger)reason;``

**Parameters:**

- ``(long long)deviceId`` - the deviceId of the speaker
- ``(NSInteger)reason`` - the reason code about the updated status

**Returns:**
	``void``
	
----

hkwErrorOccurred() - required
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Invoked when an error occures.

**Signature:**
	``-(void)hkwErrorOccurred:(NSInteger)errorCode withErrorMessage:(NSString*)errorMesg;``

**Parameters:**

- ``(NSInteger)errorCode`` - an integer value indicating error code.
- ``(NSString*)errorMesg`` - a string value containing a description about the error.

**Returns:**
	``void``
	
----

HKWPlayerEventHandlerSingleton
-------------------------------

sharedInstance()
~~~~~~~~~~~~~~~~~~~~

Returns the singleton object to HKWPlayerEventHandler. 

**Signature:**
	``+(HKWPlayerEventHandlerSingleton*)sharedInstance;``

----

delegate
~~~~~~~~~~~~~

The delegate attribute to be set by an object to be a HKWPlayerEventHandlerDelegate.

.. code-block:: objective-c

	@property (nonatomic, weak) id<HKWPlayerEventHandlerDelegate> delegate;

HKWPlayerEventHandlerDelegate
-------------------------------

hkwPlayEnded() - required
~~~~~~~~~~~~~~~~~~~~~~~~~~~

This is called when the current playback has ended.

**Signature:**
	``-(void)hkwPlayEnded;``

----

hkwDeviceVolumeChanged() - optional
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

This is called when volume level has changed for any spekaers.

**Signature:**
	``-(void)hkwDeviceVolumeChanged:(long long)deviceId deviceVolume:(NSInteger)deviceVolume withAverageVolume:(NSInteger)avgVolume;``

**Parameters:**

- ``(long long)deviceId`` - the device unique ID (long long type)
- ``(NSInteger)deviceVolume`` - the volume level of the device (speaker)
- ``(NSInteger)avgVolume`` - the average volume level
 
hkwPlaybackStateChanged() - optional
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

This is called when player state has changed during the playback.

**Signature:**
	``-(void)hkwPlaybackStateChanged:(NSInteger)playState;``

**Parameters:**

- ``(NSInteger)playState`` - The player state

----

hkwPlaybackTimeChanged() - optional
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

This is called when the current time of playback has changed. It is called every one second.

**Signature:**
	``-(void)hkwPlaybackTimeChanged:(NSInteger)timeElapsed;``

**Parameters:**

- ``(NSInteger)timeElapsed`` - the time (in second) passed since the beginning of the playback.
