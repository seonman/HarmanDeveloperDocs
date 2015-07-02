HKWHub Specification
==================================

Session Management
-------------------

Since the HKWHub app should be able to handle REST API requests from multiple clients, the HKWHub app manages the requests with session information associated with the priority when the session is initialized.

Below are the policy of the session management:

Session Creation
~~~~~~~~~~~~~~~~~
- When a client wants to send requests to HKWHub app, it must create a session first. (Use ``init_session`` command.)

Priority of Session
~~~~~~~~~~~~~~~~~~~~~
- Because the HKWHub app should handle multiple clients, each session is associated with a priority value which will be used to determine which request can override the current on-going session.
- A higher priority interrupts the current session if any.
- If a session is overridden by a higher priority, then the session is no longer valid. (the session is closed.)
- If an audio playback is running within a session, and if it is interrupted by other session, then the playback is interruped, and the playback status (see the related API in the next section) becomes ``PlayerStateInterrupted``.

Session Timeout
~~~~~~~~~~~~~~~~~
- A session becomes expired and invalid when about 60 minutes is passed since the last command was received.
- All requests with expired session will be denied and "SessionNotFound" error returns.


REST API Specification
-----------------------

Session Management
~~~~~~~~~~~~~~~~~~~~

Start Session
^^^^^^^^^^^^^^

- API: GET /v1/init_session
- Response
	- Returns a unique session id
	- The session id will be used for upcoming requests.
- Example:
	- Request: ``http://192.168.1.10/v1/init_session``
	- Response: 

.. code-block:: json

	{"SessionID" : "1000"}

Close Session
^^^^^^^^^^^^^^

- API: GET /v1/close_session?SessionID=<session id>
- Response
	- Returns true or false indicating success or failure
- Example:
	- Request: ``http://192.168.1.10/v1/close_session?SessionID=1000``
	- Response: 

.. code-block:: json

	{"Result" : "true"}

Device Management
~~~~~~~~~~~~~~~~~~~~

Get device cound
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

- API: GET /v1/device_count?SessionID=<session id>
- Response
	- Returns the number of devices connected to the network
- Example:
	- Request: ``http://192.168.1.10/v1/device_count?SessionID=1000``
	- Response: 

.. code-block:: json

	{"DeviceCount":"2"}


Get the list of Devices and their information
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

- API: GET /v1/device_list?SessionID=<session id>
- Response
	- Returns the list of devices with all the device information
- Example:
	- Request: ``http://192.168.1.10/v1/device_list?SessionID=1000``
	- Response: 

.. code-block:: json

	{"DeviceList":
		[{"GroupName":"Bathroom", 
		"Role":21, 
		"MacAddress":"b0:38:29:1b:36:1f", 
		"WifiSignalStrength":-47, 
		"Port":44055, 
		"Active":true, 
		"DeviceName":"Adapt1", 
		"Version":"0.1.6.2", 
		"ModelName":"Omni Adapt", 
		"IPAddress":"192.168.1.40", 
		"GroupID":"3431724438", 
		"Volume":47, 
		"IsPlaying":false, 
		"DeviceID":"34317244381360"
		},
		{"GroupName":"Temp", 
		"Role":21, 
		"MacAddress":"b0:38:29:1b:9e:75", 
		"WifiSignalStrength":-53, 
		"Port":44055, 
		"Active":true, 
		"DeviceName":"Adapt", 
		"Version":"0.1.6.2", 
		"ModelName":"Omni Adapt", 
		"IPAddress":"192.168.1.39", 
		"GroupID":"1293219209", 
		"Volume":47, 
		"IsPlaying":false, 
		"DeviceID":"129321920968880"
		}]
	}
	
Get the Device Information
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

- API: GET /v1/device_info?SessionID=<session id>&DeviceID=<device id>
- Response
	- Returns the information of the device
- Example:
	- Request: ``http://192.168.1.10/v1/device_info?SessionID=1000&DeviceID=129321920968880``
	- Response: 

.. code-block:: json

	{"GroupName":"Temp", 
	"Role":21, 
	"MacAddress":"b0:38:29:1b:9e:75", 
	"WifiSignalStrength":-52, 
	"Port":44055, 
	"Active":true, 
	"DeviceName":"Adapt", 
	"Version":"0.1.6.2", 
	"ModelName":"Omni Adapt", 
	"IPAddress":"192.168.1.39", 
	"GroupID":"1293219209", 
	"Volume":47, 
	"IsPlaying":true, 
	"DeviceID":"129321920968880"}

Add a Device to Session
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

- API: GET /v1/add_device_tosession?SessionID=<session id>&DeviceID=<device id>
- Response
	- Returns true or false
- Example:
	- Request: ``http://192.168.1.10/v1/add_device_to_session?SessionID=1000&DeviceID=129321920968880``
	- Response: 

.. code-block:: json

	{"Result":"true"}

Remove a Device from Session
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

- API: GET /v1/remove_device_from_session?SessionID=<session id>&DeviceID=<device id>
- Response
	- Returns true or false
- Example:
	- Request: ``http://192.168.1.10/v1/remove_device_from_session?SessionID=1000&DeviceID=129321920968880``
	- Response: 

.. code-block:: json

	{"Result":"true"}
	

Remote (Hub) Media Playback Management
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Get the list of stored media
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

- API: GET /v1/media_list?SessionID=<session id>
- Response
	- Returns JSON of the list of store media in the HKWHub app.
- Example:
	- Request: ``http://192.168.1.10/v1/media_list?SessionID=1000``
	- Response: 

.. code-block:: json

	{"MediaList": [
		{"PersistentID":"7387446959931482519",
		"Title":"I Will Run To You",
		"Artist":"Hillsong",
		"Duration":436,
		"AlbumTitle":"Simply Worship"
		},
		{"PersistentID":"5829171347867182746",
		"Title":"I'm Yours [ORIGINAL DEMO]",
		"Artist":"Jason Mraz",
		"Duration":257,
		"AlbumTitle":"Wordplay [SINGLE EP]"}
	]}

Play a Song in th Remote Hub
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

- API: GET /v1/play_hub_media?SessionID=<session id>&PersistentID=<persistent id>
- Response
	- Play a song stored in the hub, and then return true or false.
- Example:
	- Request: ``http://192.168.1.10/v1/play_hub_media?SessionID=1000&PersistentID=7387446959931482519``
	- Response: 

.. code-block:: json

	{"Result":"true"}

Pause the Current Playback
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

- API: GET /v1/pause_play?SessionID=<session id>
- Response
	- Pause the current playback, and then return true or false.
	- It can resume the current playback by calling ``resume_hub_media`` if and only if the playback is playing hub media.
- Example:
	- Request: ``http://192.168.1.10/v1/pause_play?SessionID=1000``
	- Response: 

.. code-block:: json

	{"Result":"true"}
	
Resume the Current Playback with Hub Media
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

- API: GET /v1/resume_hub_media?SessionID=<session id>&PersistentID=<persistent id>
- Response
	- Resume the current playback with Hub Media, and then return true or false.
- Example:
	- Request: ``http://192.168.1.10/v1/resume_hub_media?SessionID=1000&PersistentID=7387446959931482519``
	- Response: 

.. code-block:: json

	{"Result":"true"}

Stop the Current Playback
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

- API: GET /v1/stop_play?SessionID=<session id>
- Response
	- Stop the current playback with Hub Media, and then return true or false.
	- If the playback has stopped, then it cannot resume.
- Example:
	- Request: ``http://192.168.1.10/v1/stop_play?SessionID=1000``
	- Response: 

.. code-block:: json

	{"Result":"true"}
	

Get the Playback Status (Current Playback State and Elapsed Time)
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

- API: GET /v1/playback_status?SessionID=<session id>
- Response
	- It returns the current state of the playback and also return the elapsed time (in second) of the playback.
	- If it is not playing, then the elapsed time is (-1)
	- The following is the value of each playback state:
		- PlayerStateInitialized : Play is ready
		- PlayerStatePlaying : Now playing audio
		- PlayerStatePaused : Playing is paused. It can resume.
		- PlayerStateStopped : Playing is stopped. It cannot resume.
		- PlayerStateInterrupted : The current playback was interrupted by some reasons.
			- For example, if other client overrides the current playback by starting a new session and play, then the on-going playback is interrupted (stopped).

	- Note that if the playback has stopped, then it cannot resume.
	- Developers need to check the playback status during the playback to handle any possible exceptional cases like interruption or errors. We recommedn to call this API every second.
- Example:
	- Request: ``http://192.168.1.10/v1/playback_status?SessionID=1000``
	- Response: 

.. code-block:: json

	{"PlaybackState":"PlayerStatePlaying",
	 "TimeElapsed":"15"}


Check if the Hub is playing audio
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

- API: GET /v1/is_playing?SessionID=<session id>
- Response
	- Returns true (playing) or false (not playing)
- Example:
	- Request: ``http://192.168.1.10/v1/is_playing?SessionID=1000``
	- Response: 

.. code-block:: json

	{"IsPlaying":"true"}

Volume Control
~~~~~~~~~~~~~~~~~

Get Volume for all Devices
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

- API: GET /v1/get_volume?SessionID=<session id>
- Response
	- Returns the average volume of all devices.
	- The range of volume is 0 (muted) to 50 (max)
- Example:
	- Request: ``http://192.168.1.10/v1/get_volume?SessionID=1000``
	- Response: 

.. code-block:: json

	{"Volume":"10"}

Get Volume for a particular device
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

- API: GET /v1/get_volume_device?SessionID=<session id>&DeviceID=<device id>
- Response
	- Returns the  volume of a particular device
	- The range of volume is 0 (muted) to 50 (max)
- Example:
	- Request: ``http://192.168.1.10/v1/get_volume_device?SessionID=1000&DeviceID=1234567``
	- Response: 

.. code-block:: json

	{"Volume":"10"}

Set Volume for all devices
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

- API: GET /v1/set_volume_device?SessionID=<session id>&DeviceID=<device id>
- Response
	- Returns true or false
- Example:
	- Request: ``http://192.168.1.10/v1/get_volume_device?SessionID=1000&Volume=10``
	- Response: 

.. code-block:: json

	{"Result":"true"}
	
Set Volume for a particular device
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

- API: GET /v1/set_volume_device?SessionID=<session id>&DeviceID=<device id>&Volume=<volume>
- Response
	- Returns true or false
- Example:
	- Request: ``http://192.168.1.10/v1/get_volume_device?SessionID=1000&DeviceID=1234567&Volume=10``
	- Response: 

.. code-block:: json

	{"Result":"true"}