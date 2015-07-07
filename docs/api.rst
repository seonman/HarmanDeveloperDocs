.. _smartapp_ref:

API Documentation
===================


Initialization
------------------

The following methods should be defined by all SmartApps. They are called by the SmartThings platform at various points in the SmartApp lifecycle.

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

**Example:**

.. code-block:: groovy

    def installed() {
        log.debug "installed with settings: $settings"

        // subscribe to events, create scheduled jobs.
    }

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
    ``(NSURL*) assetURL`` - NSURL to the audio file
		
	``(NSString*) songName`` -  the song name to be played. The soneName is used internally to save a temporary PCM file convered from the original audio file
	
	``(BOOL) resumeFlag`` -  a boolean that specifies if the play resume from the point that paused or stopped in the previous playback. When starting a song from the beginning, resumeFlag must be false.

**Returns:**
	``BOOL`` - boolean value indicating success or failure
