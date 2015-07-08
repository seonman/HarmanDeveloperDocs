.. _smartapp_ref:

API Documentation
===================


Initialization
------------------

Refer to HKWControlHandler.h in the SDK.


initializeHKWirelessController()
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Initializes and starts HKWirelessHD controller. This API requires a license key as string. If the input license key fails in key validation, then the API returns -1 as result. If it is successful, return 0.

.. note::
	This is a blocking call, and it will not return until the caller successfully initializes HKWireless controller. If the phone is not connected to a Wi-Fi network, or any other app on the same phone is using the HKWireless controller, it waits until the app releases the controller.

	If you needs non-blocking behavior, you should call this API asynchronously using other thread.


**Signature:**
    ``- (NSInteger) initializeHKWirelessController:(NSString*)licenseKey;``

**Parameters:**
    ``(NSString*) licenseKey`` - a string containing the license key
	
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

Refreshes the device status only one time. The device information will be refreshed and updated in DeviceInfo objects. If there is any updates on DeviceInfo objects, then DeviceStateUpdated callback defined by registerCallbackDeviceStateUpdated will be called.

**Signature:**
	``- (void) refreshDeviceInfoOnce;``

**Returns:**
	``void``
	
----

startRefreshDeviceInfo()
~~~~~~~~~~~~~~~~~~~~~~~~

Starts to refresh devices every two seconds. It continues until stopRefreshDeviceInfo(). The result of refreshing device info is the same as described in refreshDeviceInfoOnce().

**Signature:**
	``- (void) startRefreshDeviceInfo;``
	
**Returns:**
	``void``
	
----

stopRefreshDeviceInfo()
~~~~~~~~~~~~~~~~~~~~~~~~~

Stops refreshing devices, which was initiated by startRefreshDeviceInfo().

**Signature:**
	``- (void) stopRefreshDeviceInfo;``
	
**Returns:**
	``void``

----

Playback Control
------------------

playCAF()
~~~~~~~~~

Plays a CAF audio file. CAF includes MP3 and WAV. PlaybackStateChanged callback will return the status, EPlayerState_Play.

**Signature:**
	``- (BOOL) playCAF:(NSURL *)assetURL songName:(NSString*)songName resumeFlag:(BOOL)resumeFlag;``

**Parameters:**

- ``(NSURL*) assetURL`` - NSURL to the audio file
- ``(NSString*) songName`` -  the song name to be played. The soneName is used internally to save a temporary PCM file convered from the original audio file
- ``(BOOL) resumeFlag`` -  a boolean that specifies if the play resume from the point that paused or stopped in the previous playback. When starting a song from the beginning, resumeFlag must be false.

**Returns:**
	``BOOL`` - boolean value indicating success or failure

----

playCAFFromCertainTime()
~~~~~~~~~~~~~~~~~~~~~~~~~~

Plays a CAF audio file from a certain time. CAF includes mp3, wav, and m4a. Differently from ``playCAF()``, this function allows to play a song from a certain time, specifyed by startTime (second). PlaybackStateChanged callback will return the status, EPlayerState_Play.

**Signature:**
	``- (BOOL) playCAFFromCertainTime:(NSURL *)assetURL songName:(NSString*)songName startTime:(NSInteger)startTime;``

**Parameters:**

- ``(NSURL *)assetURL`` - NSURL to the audio file.
- ``(NSString*)songName`` - the song name to be played. This information is used internally to save a temporary PCM file converted from the original audio file.
- ``(NSInteger)startTime - time in second that specifies the start time.

**Returns:**
	``BOOL`` - boolean value indicating success or failure

----

playWAV()
~~~~~~~~~~~~

Plays a WAV file. PlaybackStateChanged callback will return the status, EPlayerState_Play.

**Signature:**
	``- (BOOL) playWAV:(NSString*)wavPath;``

**Returns:**
	``BOOL`` - boolean value indicating success or failure
	
playStreamingMedia()
~~~~~~~~~~~~~~~~~~~~~~

Plays a streaming media. Note that when you stop playing the streaming music, you must use stop(), not pause().

**Signature:**
	``- (void)playStreamingMedia:(NSString *)streamingMediaUrl withCallback:(void (^)(bool result))completedCallback;``

**Parameters:**

- ``(NSString*)streamingMediaUrl`` - a string that specifies the URL of the streaming media source. It starts with a protocol name, such as "http://" or "rtps://". Currently, http, rtps, and mms are supported. The supported file format is mp3, m4a, wav.
- ``(void (^)(bool result))completedCallback`` - a callback that returns the result of the playback

**Returns:**
	``void``
	
----

pause()
~~~~~~~~~~

Pauses the current playback. PlaybackStateChanged callback will return the status, EPlayerState_Pause.

**Signature:**
	``- (void) pause;``

**Returns:**
	``void``

----

stop()
~~~~~~~~~

Stops the current playback. PlaybackStateChanged callback will return the status, EPlayerState_Stop.

**Signature:**
	``- (void) stop;``

**Returns:**
	``void``

----

isPlaying()
~~~~~~~~~~~~

Inquires whether an audio file is being played or not.

**Signature:**
	``- (bool) isPlaying;``

**Returns:**
	``BOOL`` - boolean value indicating if the audio is being played or now.

----
	
getPlayerState()
~~~~~~~~~~~~~~~~~~~

Inquires the current state of playback.

**Signature:**
	``- (HKPlayerState)getPlayerState;``
	
**Returns:**
	``HKPlayState`` - indicates the current player state.
	
----

Volume Control
----------------

setVolumeAll()
~~~~~~~~~~~~~~~~

Sets a volume level to all speakers in the network. The same volume level is set to all speakers.

The range of volume level is 0 (min) to the maximumVolumeLevel (currently, 50) defined by getMaximumVolumeLevel.

Setting volume is asynchronous call. So, the effect of the API call will occur after a few milliseconds. The VolumeLevelChanged callback defined by registerCallbackVolumeLevelChanged() will be called when the volume level of the specified speaker has changed.

If the volume is being muted, the volume becomes unmuted first, and then set the volume.

**Signature:**
	``- (void) setVolumeAll:(NSInteger)volume;``

**Parameters:**

- ``(NSInteger)volume`` -  the volume level to set

**Returns:**
	``void``

----

setVolumeDevice()
~~~~~~~~~~~~~~~~~~~~
Set a volume level to an individual speaker specified by deviceId. The range of volume level is 0 (min) to the maximumVolumeLevel (currently, 50) defined by getMaximumVolumeLevel. setVolume is asynchronous call. So, the effect of the API call will occur after a few milliseconds. The VolumeLevelChanged callback defined by registerCallbackVolumeLevelChanged() will be called when the volume level of the specified speaker has changed.<p>If the volume is being muted, the volume becomes unmuted first, and then set the volume.

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
Returns the maximum volume level that the system provides.

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

addDeviceToSession()
~~~~~~~~~~~~~~~~~~~~~~~

removeDeviceFromSession()
~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Device (Speaker) Management
------------------------------

getDeviceCount()
~~~~~~~~~~~~~~~~~~

getGroupCount()
~~~~~~~~~~~~~~~~~

getDeviceCountInGroupIndex()
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

getDeviceInfoFromTable()
~~~~~~~~~~~~~~~~~~~~~~~~~~~

getDeviceInfoByIndex()
~~~~~~~~~~~~~~~~~~~~~~~~~

findDeviceGroupWithDeviceId()
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

findDeviceFromList()
~~~~~~~~~~~~~~~~~~~~~~~

isDeviceActive()
~~~~~~~~~~~~~~~~~~~


removeDeviceFromGroup()
~~~~~~~~~~~~~~~~~~~~~~~~~~~


getDeviceGroupByIndex()
~~~~~~~~~~~~~~~~~~~~~~~~~

getDeviceGroupById()
~~~~~~~~~~~~~~~~~~~~~~~

getDeviceGroupNameByIndex()
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

getDeviceGroupIdByIndex()
~~~~~~~~~~~~~~~~~~~~~~~~~~~~

setDeviceName()
~~~~~~~~~~~~~~~~~~

setDeviceGroupName()
~~~~~~~~~~~~~~~~~~~~~~

setDeviceRole()
~~~~~~~~~~~~~~~~~

getActiveDeviceCount()
~~~~~~~~~~~~~~~~~~~~~~~~

getActiveGroupCount()
~~~~~~~~~~~~~~~~~~~~~~~

refreshDeviceWiFiSignal()
~~~~~~~~~~~~~~~~~~~~~~~~~~~

getWifiSignalStrengthType()
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~


