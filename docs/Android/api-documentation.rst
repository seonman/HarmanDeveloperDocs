API Documentation (Android)
=============================



Initialization
--------------------

initializeHKWirelessController()
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Initializes and starts HKWirelessHD controller. This API requires a license key as string. If the input license key fails in key validation, then the API returns -1. If it is successful, return 0.

The license key will be delivered to you once you register on developer.harman.com. Until it is delivered, you may use the license key included in the sample apps.

Note

This is a blocking call, and it will not return until HKWireless controller initialized. If the phone is not connected to a Wi-Fi network, or any other app in the same phone is using the HKWireless controller, it waits until the other app releases the controller.

If you need non-blocking behavior, you should call this API asynchronously using other thread.

**Signature:**

``int initializeHKWirelessController(String key)``

**Parameters:**

``String key`` - the license key

**Returns:**

``int`` - success (0 or HKW_INIT_SUCCESS) or failure (-1 or HKW_INIT_FAILURE_LICENSE_INVALID)


isInitialized()
^^^^^^^^^^^^^^^^^^

Checks if HKWirelessHD controller is initialied.

**Signature:**

``boolean isInitialized()``

**Returns:**

``boolean`` -  True if HKWirelessController has been initialized, false not intialized.


Refreshing Speaker Information
--------------------------------

refreshDeviceInfoOnce()
^^^^^^^^^^^^^^^^^^^^^^^^^^

Refresh the devices one time. The device information will be refreshed and updated to DeviceInfo objects. If there is any update the onDeviceStateUpdated function will be called. you can find which device has been updated and what was the reason.

**Signature:**

``void refreshDeviceInfoOnce()``

**Returns:**

``void``

startRefreshDeviceInfo()
^^^^^^^^^^^^^^^^^^^^^^^^^^
Start to refresh DeviceInfo every two seconds, until ``stopRefreshDeviceInfo()`` is called.

**Signature:**

``void startRefreshDeviceInfo()``

**Returns:**

``void``

stopRefreshDeviceInfo()
^^^^^^^^^^^^^^^^^^^^^^^^^^
Stop refreshing DeviceInfo, which was started by ``startRefreshDeviceInfo()``.

**Signature:**

``void stopRefreshDeviceInfo()``

**Returns:**

``void``

Playback Control
--------------------------------

playCAF()
^^^^^^^^^^^^^^^^^^^^^^^^^^

Play an audio file in local storage. If it is successful, ``onPlaybackStateChanged()`` function will be called and return the status value of HKPlayerState.EPlayerState_Play.

Currently, it supports formats of mp3, wav, flac, sac, m4a and ogg, and the sample rate must be 44100 and above. playCAF is used to play mp3, wav, flac, sac, m4a or ogg file, and playWAV is only for WAV file.
**Signature:**

``boolean playCAF(String url, String songName, boolean resumeFlag)``

**Parameters:**

``String url`` - Path of audio file

``String songName`` - the song name is used internally to save the temporary PCM file generated from the original audio file.

``boolean resumeFlag`` -  If the playback should resume from the paused point . ResumeFlag should be false if start the song from the beginning, and if the playback was stopped, then the playback will start from the beginning even with resumeFlag true.

Returns:

``boolean`` - Success or failure

playCAFFromCertainTime()
^^^^^^^^^^^^^^^^^^^^^^^^^^

Play an audio file from a certain time specified by startTime. For example, you can start a song from the point of 10 seconds of the song. ``onPlaybackStateChanged()`` function will be called and return the status value of HKPlayerState.EPlayerState_Play.

**Signature:**

``boolean playCAFFromCertainTime(String url, String songName, int startTime)``

**Parameters:**

``String url`` - Path of audio file.

``String songName`` - the song name is used internally to save the temporary PCM file generated from the original audio file.
int startTime - time in seconds that specifies the start time.

**Returns:**

``boolean`` - Success or failure

playWAV()
^^^^^^^^^^^^^^^^^^^^^^^^^^

Play a WAV file. onPlaybackStateChanged function will be called and return the status of HKPlayerState.EPlayerState_Play.

**Signature:**

``boolean playWAV(String url)``

**Parameters:**

``String url`` - Path of the wav file

**Returns:**

``boolean`` - Success or failure

playStreamingMedia()
^^^^^^^^^^^^^^^^^^^^^^^^^^

Plays a streaming media from web server. The API is a synchronous call. It will not return until the caller succeeds or fails. You can find the error message from the function of ``onErrorOccurred()``

**Signature:**

``void playStreamingMedia(String streamingMediaUrl)``

**Parameters:**

``String streamingMediaUrl`` - The URL of the streaming media source. It starts with a protocol name, such as http://. Currently, only http is supported. 

**Returns:**

``void``

pause()
^^^^^^^^^^^^^^^^^^^^^^^^^^

Pause current playing. ``onPlaybackStateChanged()`` function will be called and return the status of HKPlayerState.EPlayerState_Pause. Once the playback is paused, it can be resumed by ``playCAF()`` with resumeFlag to be true and the same songName. the playback is stopped by pause, if it was played by ``playStreamingMedia()``.

**Signature:**

``void pause()``

**Returns:**

``void``

stop()
^^^^^^^^^^^^^^^^^^^^^^^^^^

Stop playing. ``onPlaybackStateChanged()`` function will be called and return the status of HKPlayerState.EPlayerState_Stop.

**Signature:**

``void stop()``

**Returns:**

``void``

isPlaying()
^^^^^^^^^^^^^^^^^^^^^^^^^^

Check if the player is playing or not.

**Signature:**

``boolean isPlaying()``

**Returns:**

``boolean`` - boolean value indicating if the player is playing or not.

getPlayerState()
^^^^^^^^^^^^^^^^^^^^^^^^^^

Get current state of playback.

**Signature:**

``HKPlayerState getPlayerState()``

**Returns:**

``HKPlayerState`` - Current player state.


Volume Control
---------------------

setVolumeAll()
^^^^^^^^^^^^^^^^^^^^^^^^^^
Set a volume level to all speakers.

The range of volume level is 0 to the maximumVolumeLevel (currently, 50) by ``getMaximumVolumeLevel()``.

It is asynchronous call. The volume will be set within a few milliseconds. The ``onVolumeLevelChanged()`` function defined in HKWirelessListener will be called when volume of specified speaker has been changed.

**Signature:**

``void setVolumeAll(int volume)``

**Parameters:**

``int volume`` - the volume level

**Returns:**

``void``


setVolumeDevice()
^^^^^^^^^^^^^^^^^^^^^^^^^^

Set a volume level to a speaker specified by device ID. The range of volume level is 0 to the maximumVolumeLevel (currently, 50) by ``getMaximumVolumeLevel()``. It is asynchronous call. The effect of the API call will occur after a few milliseconds. The ``onVolumeLevelChanged()`` function will be called when volume of specified speaker has been changed.

**Signature:**

``void setVolumeDevice(long deviceId, int volume)``

**Parameters:**

``long deviceId`` - Device ID of the speaker

``int volume`` - Volume level

**Returns:**

``void``

getVolume()
^^^^^^^^^^^^^^^^^^^^^^^^^^

Get the average volume level for all devices.

**Signature:**

``int getVolume()``

**Returns:**

``int`` - the average volume level of all speakers

getDeviceVolume()
^^^^^^^^^^^^^^^^^^^^^^^^^^

Get the volume level of the specified speaker.

**Signature:**

``int getDeviceVolume(long deviceId)``

**Parameters:**

``long deviceId`` - Device Id of the speaker.

**Returns:**

``int`` - Volume level

getMaximumVolumeLevel()
^^^^^^^^^^^^^^^^^^^^^^^^^^

Return the maximum volume level that the system provides. Currently, it is 50.

**Signature:**

``int getMaximumVolumeLevel()``

**Returns:**

``int`` - the maximum volume level


Device (Speaker) Management
------------------------------

addDeviceToSession()
^^^^^^^^^^^^^^^^^^^^^^^^^^

Add a speaker to the current playback session. The added speaker will start playing. This can be called during playing.

**Signature:**

``boolean addDeviceToSession(long id)``

**Parameters:**

``long deviceId`` - Device Id of the speaker.

**Returns:**

``boolean`` - Whether the addition is successful or not.

removeDeviceFromSession()
^^^^^^^^^^^^^^^^^^^^^^^^^^

Remove a speaker from the current playback session. The removed speaker will stop playing. This can be called during playing.

**Signature:**

``boolean removeDeviceFromSession(long deviceid)``

**Parameters:**

``long deviceId`` - Device Id of the speaker.

**Returns:**

``boolean`` - Whether the removal is successful or not.

getDeviceCount()
^^^^^^^^^^^^^^^^^^^^^^^^^^

Get the number of speakers in the HKWirelessHD network.

**Signature:**

``int getDeviceCount()``

**Returns:**

``int`` - the number of speakers.

getGroupCount()
^^^^^^^^^^^^^^^^^^^^^^^^^^

Get the number of groups.

**Signature:**

``int getGroupCount()``

**Returns:**

``int`` - the number of groups

getDeviceCountInGroupIndex()
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Get the number of speakers in the group specified by the index.

**Signature:**

``int getDeviceCountInGroupIndex(int groupIndex)``

**Parameters:**

``int groupIndex`` - the index of the group looking for. It starts from 0 to (GroupCount-1).

**Returns:**

``int`` - the number of speakers

getDeviceInfoFromTable()
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Return the DeviceInfo of specified device in specified group. 

**Signature:**

DeviceObj getDeviceInfoFromTable(int groupIndex, int deviceIndex)

**Parameters:**

``int groupIndex`` - group index in the group list.

``int deviceIndex`` - The index of the speaker in the group.

**Returns:**

``DeviceObj`` - the device information

getDeviceInfoByIndex()
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Return the DeviceInfo of specified device from the global table from 0 to (deviceCount - 1).

**Signature:**

``DeviceObj getDeviceInfoByIndex(int deviceIndex)``

**Parameters:**

``int deviceIndex`` - The index of the device from the table with all devices.

**Returns:**

``DeviceObj`` - the device information

getDeviceGroupByDeviceId()
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Return the DeviceGroup the speaker belongs to.

**Signature:**

``GroupObj findDeviceGroupWithDeviceId(long deviceId)``

**Parameters:**

``long deviceId`` - the device ID

**Returns:**

``GroupObj`` - the group information

getDeviceInfoById()
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Find the a DeviceInfo by device Id. It is used to retrieve the DeviceInfo of specified device.

**Signature:**

``DeviceObj getDeviceInfoById(long deviceId)``

**Parameters:**

``long deviceId`` - the device ID.

**Returns:**

``DeviceObj`` - the device information

isDeviceAvailable()
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Check whether the specified speaker is available or not.

**Signature:**

``boolean isDeviceAvailable(long deviceId)``

**Parameters:** 

``long deviceId`` - the device ID.

**Returns:**

``boolean`` - the speaker is available or not.

isDeviceActive()
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Check whether the speaker is active (added to the current playback session) or not.

**Signature:**

``boolean isDeviceActive(long deviceId)``

**Parameters:**

``long deviceId`` - The ID of the speaker

**Returns:**

``boolean`` - if the speaker is active or not.

removeDeviceFromGroup()
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Remove the specified speaker from its group. 

**Signature:**

``void removeDeviceFromGroup(long groupId, long deviceId)``

**Parameters:**

``long groupId`` - group ID

``long deviceId`` - device ID

**Returns:**

``void``

getDeviceGroupByIndex()
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Get the group information.

**Signature:**

``GroupObj getDeviceGroupByIndex(int groupIndex)``

**Parameters:**

``int groupIndex`` - group index

**Returns:**

``GroupObj`` - the group information

getDeviceGroupById()
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Get the group information by group ID.

**Signature:**

``GroupObj getDeviceGroupById(long groupId)``

**Parameters:**

``long groupId`` - the ID of the group

**Returns:**

``GroupObj`` - the group information

getDeviceGroupNameByIndex()
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Get the name of the DeviceGroup by index.

**Signature:**

``String getDeviceGroupNameByIndex(int groupIndex)``

**Parameters:**

``int groupIndex`` - the index of the group in the group table.

**Returns:**

``String`` - the string of group name

getDeviceGroupIdByIndex()
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Gets the ID of the DeviceGroup by index.

**Signature:**

``long getDeviceGroupIdByIndex(int groupIndex)``

**Parameters:**

``int groupIndex`` - the index of the group in the table

**Returns:**

``long`` - the group id

setDeviceName()
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Set device name. 

Note

If you set a device name while it is playing, the speaker will stop playing and return error.

**Signature:**

``void setDeviceName(long deviceId, String deviceName)``

**Parameters:**

``long deviceId`` - The ID of the device

``String deviceName`` - The name of the device to set

**Returns:**

``void``

setDeviceGroupName()
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Set group name. 

Note

If you set a group name while the a playback is running, the speaker stops playing and return error.

**Signature:**

``void setDeviceGroupName(long deviceId, String groupName)``

**Parameters:**

``long deviceId`` - The device ID within a group

``String groupName`` - the group name

**Returns:**

``void``

getActiveDeviceCount()
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Get the number of active speakers (the speakers that are added to the current playback session.)

**Signature:**

``int getActiveDeviceCount()``

**Returns:**

``int`` - the number of active devices

getActiveGroupCount()
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Get the number of active groups. An active group is the one that all the speakers in the group are active. 

**Signature:**

``int getActiveGroupCount()``

**Returns:**

``int`` - the number of active groups

refreshDeviceWiFiSignal()
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Refresh the devices Wifi Signal strength value. This is asynchronous call, and the result of refreshing will come a few milliseconds later. The new WiFi signal strength value will be reported by ``onDeviceStateUpdated()``. You should get the wifi signal value by getting wifiSignalStrength attribute of DeviceInfo object.

**Signature:**

``void refreshDeviceWiFiSignal(long deviceId)``

**Parameters:**

``long deviceId`` - the device ID

**Returns:**

``void``

getWifiSignalStrengthType()
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Get Wifi signal strength type by signal value.

**Signature:**

``HKWifiSingalStrength getWifiSignalStrengthType(int wifiSignal)``

**Parameters:**

``int wifiSignal`` - the wifi signal value

**Returns:**

``HKWifiSingalStrength`` - Wifi signal strength type


APIs for events handling
---------------------------------------

onDeviceStateUpdated()
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The function is called when devices status is changed, including active or inactive, model name, group name, wifi strength, etc.

Note if the volume level changed, VolumeLevelChanged function would be called instead.

This function is essential to retrieve and update speaker information in timely manner. If you want to show the available speakers and its latest information, you could get from the function in timely manner.

**Signature:**

``void onDeviceStateUpdated(long deviceId, int reason)``

**Parameters:**

``long deviceId`` - the device ID

``int reason`` - the reason code

**Returns:**

``void``

onErrorOccurred()
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The function is invoked when an error occurs. The function returns the error code and error message.


**Signature:**

``void onErrorOccurred(int errorCode, String errorMesg)``

**Parameters:**

``int errorCode`` - error code

``String errorMesg`` - error description

**Returns:**

``void``


onPlayEnded
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

This function is invoked when the current playback has ended.

**Signature:**

``void onPlayEnded()``

**Returns:**

``void``

onVolumeLevelChanged()
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

This function is invoked when volume level is changed.

The function delivers the device ID device volume level, and average volume level.

**Signature:**

``void onVolumeLevelChanged(long deviceId, int deviceVolume, int avgVolume)``

**Parameters:**

``long deviceId`` - the device ID

``int deviceVolume`` - the volume level

``int avgVolume`` - the average volume level

**Returns:**

``void``

onPlaybackStateChanged()
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

This function is invoked when playback state is changed. The function delivers the playState.

**Signature:**

``void onPlaybackStateChanged(int playState)``

**Parameters:**

``int playState`` - The player state

**Returns:**

``void``
	
onPlaybackTimeChanged()
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

This function is invoked when current playback time is changed. It is called every second. The function parameter timeElapsed returns the time (in seconds) elapsed since the start. It is useful when you update the progress bar.

**Signature:**

``void onPlaybackTimeChanged(int timeElapsed)``

**Parameters:**

``int timeElapsed`` - the time (in second) passed since the beginning of the playback.

**Returns:**

``void``