HKWHub App - Making your Omni speakers connected from any devices (sensors) or services (iOS)
==============================================================================================

Overview of HWKHub
---------------------

HKWHub app is an iOS that that uses HKWirelessHD SDK and acts as a Web Hub that handles HTTP requests to control speakers and stream music. It enables any type of "connected" devices (like sensors or smart devices) and cloud-based service to connect HK Omni speakers and stream music.

.. note::

	Please note that HKWHub app is an on-going project, and not yet ready for production. We hope developers play around with this app and implement their own apps or services by integrating many other IoT devices or services and adding intelligence to the Hub app.
	
	Any feedback or request for new APIs and features are always welcome.



Use Cases
~~~~~~~~~~~~

.. figure:: img/hub/hub-use-cases.png


HKWHub App 
~~~~~~~~~~~~

Web Hub handles all the requests from and response to smart devices, sensors or clouds to control audio playback with wireless speakers in the house.

Features
^^^^^^^^^
- Supports integration with cloud-based services, smart devices or sensors
	- Receives the requests from clouds (web service) outside or sensors in the house
	- Translates the requests into HKWirelessHD commands and controls the speakers based on the requests.
	- Sends response with status of speakers to the cloud if necessary 
- Central Music Playlist manager
	- Maintain user’s media list from iOS local music library or streaming services, like MixRadio, etc.
	- Maintain a collection of sound files used for IoT use cases, like door bell, etc.

Usage
^^^^^^^^
- User may place an iOS device on the cradle and run WebHub app. Then the app acts as Web Hub. (A stationary device in a house lik AppleTV can be a nice iOS device for WebHub.)


.. figure:: img/hub/hub-app.png

Overall Architecture
~~~~~~~~~~~~~~~~~~~~~~~

HKWHub App handles requests from and sends responses to sensors, smart devices or cloud-based services to control audio playback with wireless speakers in the house.

.. figure:: img/hub/architecture.png


The latest version of HKWHub app supports the following three modes:

- Cloud mode (HKIoTCloud)
	- HKWHub app communicates with HKIoTCloud to receive speaker control commands by REST API call from 3rd party services or clients.
	- HKIoTCloud handles the REST API request from any clients in the Internet. The clients can be 3rd party apps or services or devices like smartphone or sensors.
	- In this mode, any 3rd party services or clients in the Internet can reach out to HKWHub app and then control speakers and playback of audio.

- Local Server Mode
	- HKWHub app lauches a web server internally, and then handles the REST API requests for speaker control and playback from devices, sensors or applications in the same local network. 
	- HKWHub app opens a HTTP port in the local network, so if devices or services outside of the local network want to reach out to HKWHub (and then speakers) then user needs to configure the route so that a request coming from outside can be routed to HKWHub app accordingly, such as firewall, etc.

- PubNub Cloud mode
	- HKWHub app uses PubNub API/SDK to connect to PubNub server and communicate with it to receive commands from other PubNub clients, and also sends events to other PubNub client, through a common PubNub channel.
	- By setting the same PubNub channel, any client devices or services can communicate with the HKWHub app, and then control speakers and playback of audio.
	
The following figure shows how HKWHub app handles three modes.

.. figure:: img/hub/HubAppV2.png

----

Quick Getting Started Guide to HKWHub App
-------------------------------------------

Overview of HKWHubApp 
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Main Screen
^^^^^^^^^^^^^

.. figure:: img/hub/hubappv2-main.jpg
	:scale: 30
	
The main screen is composed of two parts - **Select Server mode** (amonng HKIoTCloud, Local Server and PubNub Cloud), and **Settings**.

The **Select Server mode** has three options:

- Connect to HK IoT Cloud
	- HKWHub app connects to HKIotCloud, and communicate with it with WebSocket to receive REST API commands from and send the responses back to the Cloud.
- Run Local Web Server
	- HKWHub app runs a local web server and processes incoming REST requests to control speakers and playback of audio
- Connect to PubNub Cloud
	- HKWHub app uses PubNub APIs to connect PubNub server and communicate with other PubNub client through a common channel.
	
The **Settings** menu has four sub menus:

- Media List
	- User can maintain the list of audio files for audio playback. 
	- User can add audio from iOS Media Library. 
	.. Note::
		
		Note that only the media file available offline and not from Apple Musica can be added. The music file that came from Apple Music cannot be added by DRM issue.
			
	.. figure:: img/hub/hubappv2-medialist.jpg
		:scale: 30
		

- Set API Keys
	- To use PubNub mode, user needs to enter PubNub API keys. It requires Publish Key and Subscribe Key. And also, user needs to set the channel where it exchanges the command and events with other clients.
	- If user (or developer) wants to use TTS APIs such as **play_tts**, then user needs to enter VoiceRSS (http://www.voicerss.org) API keys. You can get a free API key.
		
	.. figure:: img/hub/hubappv2-apikeys.jpg
		:scale: 30
		
- Speaker List
	- You can see the list of speakers available in the current local network.
	- You can also change the device name or group name from this screen.
		
	.. figure:: img/hub/hubappv2-speakers.jpg
		:scale: 30
			
- About
	- The information of the app and the links to Harman developer documentation site.



	
From now on, we will explain a little more detail about each server mode.

HKIoTCloud Mode
~~~~~~~~~~~~~~~~~~~

In HKIoTCloud demo, 3rd party clients can connect to HKIoTCloud (http://hkiotcloud.herokuapp.com) and send REST requests to control speakers and play audio. In order to use HKIoTCloud mode, user needs to sign up to the cloud with username, emaill address and password. Once sign up is done, user need to sign in to the server. User sign-up and sign-in can be done within the HKWHub app, as shown below.
	
.. figure:: img/hub/hubappv2-signin.jpg
	:scale: 30

Once the HKWHub app successfully signs in to HKIoTCloud, the screen will be switched to Log screen, like shown as below. You can see all the message logs received from or sent to the cloud. Each log shows the JSON data, so you can see what information is being sent and received with the server.

.. figure:: img/hub/hubappv2-signin.jpg
	:scale: 30
	

The same username and password are required from the client side. For example, HKIoTCloud also hosts a Web-based client app, at http://hkiotcloud.herokuapp.com/webapp/. The following is a screenshot of the web app.

.. figure:: img/hub/hubappv2-hkiotcloud.jpg
	:scale: 30

If you want to disconnect the server and return to the main screen, press **Disconnect** button on the top righthand corner.

.. Note::
		
	To use this mode, user needs to sign up to HKIoTCloud with username, email, and password. 



.. Note::

	Please check out `HKWHub app demo video`_.

.. _`HKWHub app demo video`: https://www.youtube.com/watch?v=_ljpReAvk0g&feature=youtu.be


Run HKWHub app on your iOS device
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. figure:: img/hub/hub-first-run.png
	:scale: 50


Copy the URL information (http://192.168.1.192:8080/). This is the HTTP server address. You need to use this URL for your every REST API request.

Go to Setup (via Hamburger icon) menu, and add music items from iOS Music library. 

.. figure:: img/hub/medialist.png
	:scale: 30

Use ``curl`` command or web browser to send REST requests
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Use **curl** command in your shell, or use your web browser to send  HTTP requests to the server URL. In this example, we will use **curl** commands.

a. Init session
^^^^^^^^^^^^^^^
``curl "http://192.168.1.192:8080/v1/init_session"``

This returns the session id. The returned SessionID is used in all subsequent REST API commands.

.. code:: json

	{"SessionID":"1000"}


b. Add alls speaker to session
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

After HKWHub app is launched, none of speakers is selected for playback. You need to add one or more speakers to play audio. To add all speakers to playback session, use ``set_party_mode``. **Party Mode** is the mode where all speakers are playing the same audio together with synchronization. So, by ``set_party_mode``, you can select all speakers to play.

``curl "http://192.168.1.192:8080/v1/set_party_mode?SessionID=1000"``
	
	
c. Add a speaker to session
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

If you want to add a speaker to session, use ``add_device_to_session`. It requires ``DeviceID`` parameter to identify a speaker to add. This command does not impact other speakers regardless of their status.

``curl "http://192.168.1.192:8080/v1/add_device_to_session?SessionID=1000&DeviceID="34317244381360"``


d. Get the media list
^^^^^^^^^^^^^^^^^^^^^^^
``curl "http://192.168.1.192:8080/v1/media_list?SessionID=1000"``

Here, SessionID should be the session id you got from ``init_session``. You will get a list of media in JSON like below

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

e. Play a media item listed in the HKWHub app
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
If you want to play a media item listed in the HKWHub app, use ``play_hub_media`` by specifying the media item with ``PersistentID``. The ``PersistentID`` is available from the response of ``media_list`` command.

.. note::

	Note that, before calling ``play_hub_media``, at least one or more speakers must be selected (added to session) in advance. If not, then the playback will fail. 

``curl "http://192.168.1.192:8080/v1/play_hub_media?SessionID=1000&PersistentID=5829171347867182746"``

	
f. Play a media item in the HKWHub by specifying a speaker list to play
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
You can play a media item in the HKWHub app by specifying the list of speakers.

``curl "http://192.168.1.192:8080/v1/play_hub_media_selected_speakers?SessionID=1000&PersistentID=5829171347867182746&DeviceIDList=34317244381360,129321920968880"``

The list of speakers are listed by the parameter ``DeviceIDList`` with delimitor ",".

g. Play a HTTP streaming media
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
``curl "http://192.168.1.192:8080/v1/play_web_media?SessionID=1000&MediaUrl=http://seonman.github.io/music/hyolyn.mp3"``

h. Stop playing
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
``curl "http://192.168.1.192:8080/v1/stop_play?SessionID=1000"``

i. Set Volume
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
``curl "http://192.168.1.192:8080/v1/set_volume?SessionID=1000&Volume=30"``

.. note::

	Please see the REST API specification for more information and examples.

Use ``WebHubWebApp`` to play music
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
The source code of HKWHub app also contains a Web app to test the Hub app. The UI is created using Polymer v0.5 (https://www.polymer-project.org/0.5/). 

- Unzip WebHubWebApp.zip. You will see the following sub directories.
	- bower_components : This is the folder for polymer library
	- hkwhub : this is the folder containing the WebHubApp that we will run.

.. code-block:: shell

	$ cd WebHubWebApp
	$ python -m SimpleHTTPServer
	
You will get some log messages like "Serving HTTP on 0.0.0.0 port 8000 ..."

Next, launch your web browser (Chrome, Safari, ...) and go to http://localhost:8000/hkwhub/

.. note::

	Your iOS device running HKWHub app and your Desktop PC running web browser should be in the same network.

At the fist screen looking like this:

.. figure:: img/hub/hub-first-screen.png

Enter the URL that the HKWHub app says: http://192.168.1.192:8080/, like this:

.. figure:: img/hub/hub-enter-url.png

If you press **Submit**, then you will see the first screen like below. This is the list of media items available at the HKWHub app. 

.. figure:: img/hub/hub-playlist.png

Now you can click one of items to play audio, and then move to **Media Player** tab to control playback (pause/resume/stop) and control volume. 

.. figure:: img/hub/hub-media-player.png

In **Speaker List** tab, you can select and turn on/off speaker.

.. figure:: img/hub/hub-speaker-list.png


Session Management
-------------------

Since the HKWHub app should be able to handle REST HTTP requests from more than one clients at the same time, the HKWHub app manages the requests with session information associated with the priority when the session is initialized.

The following is the policy of the session management:

Session Creation
~~~~~~~~~~~~~~~~~
- When a client wants to access speakers through the HKWHub app, it must create a session first. (Use ``init_session`` command.)
- When a client initializes session, it also sets the priority of the session (using ``Priority=<priority value>`` parameter).

Priority of Session
~~~~~~~~~~~~~~~~~~~~~
- Each session is associated with a priority value which will be used to determine which request can override the current on-going playback session.
- The priority value is specified as parameter (``Priority``) when the client calls ``init_session``.
	- If the command does not specify the Priority parameter, 100 is set as default value.
- If the priority of the session of a new playback request, such as ``play_hub_media`` or ``play_web_media``, and so on, is greater than or equal to the priority of the current session, then it interrupts the current playback session, that is, stops the current playback session and start a new playback for itself.
	- The playback status of the interrupted session becomes ``PlayerStateStopped``. (see the related API in the next section)
	- Even if a session is interrupted by a higher or equal priority playback request, the session is still valid. So, it can continue to send requests with the session id.
	
The following diagrams show how HKWHub app handles incoming playback request based on the session priorities.

.. figure:: img/hub/session-management.png
	:alt: Session management flow diagram

Session Timeout
~~~~~~~~~~~~~~~~~
- A session becomes expired and invalid when about 60 minutes is passed since the last command was received.
- All requests with expired session will be denied and "SessionNotFound" error returns.



----

REST API Specification
-----------------------

This specification describes the REST API for controlling HKWHub app remotely to control HK Omni speakers and stream audio to the speakers.

All the APIS are in REST API protocol.

Session Management
~~~~~~~~~~~~~~~~~~~~

Start Session
^^^^^^^^^^^^^^
Starts a new session. A session expires after 60 minutes of the last commands used with the SessionID. That is, the timeout restarts as a new REST command is handled by the HKWHub app. 

- API: GET /v1/init_session?Priority=<priority value>
- Response
	- Returns a unique session id
	- The session id will be used for upcoming requests.
- Example:
	- Request: ``http://192.168.1.10:8080/v1/init_session?Priority=100``
	- Response: 

.. code-block:: json

	{"SessionID" : "1000"}

----

Close Session
^^^^^^^^^^^^^^
Close the session. The SessionID information is removed from the session database.

- API: GET /v1/close_session?SessionID=<session id>
- Response
	- Returns true or false indicating success or failure
- Example:
	- Request: ``http://192.168.1.10:8080/v1/close_session?SessionID=1000``
	- Response: 

.. code-block:: json

	{"Result" : "true"}

----

Device Management
~~~~~~~~~~~~~~~~~~~~

Get the device count
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Returns the number of speakers.

- API: GET /v1/device_count?SessionID=<session id>
- Response
	- Returns the number of devices connected to the network
- Example:
	- Request: ``http://192.168.1.10:8080/v1/device_count?SessionID=1000``
	- Response: 

.. code-block:: json

	{"DeviceCount":"2"}

----


Get the list of devices and their information
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

- API: GET /v1/device_list?SessionID=<session id>
- Response
	- Returns the list of devices with all the device information
- Example:
	- Request: ``http://192.168.1.10:8080/v1/device_list?SessionID=1000``
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

----

Get the Device Information
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

- API: GET /v1/device_info?SessionID=<session id>&DeviceID=<device id>
- Response
	- Returns the information of the device
- Example:
	- Request: ``http://192.168.1.10:8080/v1/device_info?SessionID=1000&DeviceID=129321920968880``
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

----

Add a Device to Session
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Add a speaker to playback session. Once a speaker is added, then the speaker will play the music. There is no impact of this call to other speakers.

- API: GET /v1/add_device_tosession?SessionID=<session id>&DeviceID=<device id>
- Response
	- Returns true or false
- Example:
	- Request: ``http://192.168.1.10:8080/v1/add_device_to_session?SessionID=1000&DeviceID=129321920968880``
	- Response: 

.. code-block:: json

	{"Result":"true"}

----

Remove a Device from Session
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Removes a speaker from playback session. Once a speaker is removed, then the speaker will not play the music. There is no impact of this call to other speakers.

- API: GET /v1/remove_device_from_session?SessionID=<session id>&DeviceID=<device id>
- Response
	- Returns true or false
- Example:
	- Request: ``http://192.168.1.10:8080/v1/remove_device_from_session?SessionID=1000&DeviceID=129321920968880``
	- Response: 

.. code-block:: json

	{"Result":"true"}
	

Set party mode
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Addes all speakers to playback session. Once it is done, all speakers will play music.

- API: GET /v1/set_party_mode?SessionID=<session id>
- Response
	- Returns true or false
- Example:
	- Request: ``http://192.168.1.10:8080/v1/set_party_mode?SessionID=1000``
	- Response: 

.. code-block:: json

	{"Result":"true"}
	
----

Media Playback Management
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Get the list of media item in the Media List of the HKWHub app
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Returns the list of media items added to the Media List of the app. User can add music items to the **Media List** of the app via **Setting** of the app.

.. Note::

	A music item downloaded from Apple Music is not supported. The music file from Apple music is DRM-enabled, and cannot be played with HKWirelessHD. Only music items purchased from iTunes Music or added from user's own library are supported.

	To be added to the Media List, the music item must be located locally on the device. No streaming from iTunes or Apple Music are supported.


- API: GET /v1/media_list?SessionID=<session id>
- Response
	- Returns JSON of the list of store media in the HKWHub app.
- Example:
	- Request: ``http://192.168.1.10:8080/v1/media_list?SessionID=1000``
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

----

Play a song in the Media List of the HKWHub app
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Plays a song in the Media List of the Hub app. Each music item is identified with MPMediaItem's PersistentID. It is a unique ID to identify a song in the iOS Music library.

.. note::

	``play_hub_media`` does not specify speakers to play. It just uses the current session setting. If there is no speaker in the current session, then the play fails.


- API: GET /v1/play_hub_media?SessionID=<session id>&PersistentID=<persistent id>
- Response
	- Play a song stored in the hub, and then return true or false.
- Example:
	- Request: ``http://192.168.1.10:8080/v1/play_hub_media?SessionID=1000&PersistentID=7387446959931482519``
	- Response: 

.. code-block:: json

	{"Result":"true"}

----

Play a song in the Media list as party mode
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Plays a song in the Media List with all speakers available. So, regardless of current session setting, this command play a song to all speakers.

- API: GET /v1/play_hub_media_party_mode?SessionID=<session id>&PersistentID=<persistent id>
- Response
	- Play a song in the hub's media list to all speakers, and then return true or false.
- Example:
	- Request: ``http://192.168.1.10:8080/v1/play_hub_media_party_mode?SessionID=1000&PersistentID=7387446959931482519``
	- Response: 

.. code-block:: json

	{"Result":"true"}

----

Play a song in the Media list with selected speakers
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Plays a song in the Media List with selected speakers. The selected speakers are represented in ``DeviceIDList`` parameter as a list of ``DeviceID`` separated by ",".

- API: GET /v1/play_hub_media_selected_speakers?SessionID=<session id>&PersistentID=<persistent id>&DeviceIDList=<xxx,xxx,...>
- Response
	- Play a song in the hub's media list to selected speakers, and then return true or false.
- Example:
	- Request: ``http://192.168.1.10:8080/v1/play_hub_media_selected_speakers?SessionID=1000&PersistentID=7387446959931482519&DeviceIDList=34317244381360,129321920968880``
	- Response: 

.. code-block:: json

	{"Result":"true"}

----

Play a Song from Web Server
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Plays a song from Web (http:) or rstp (rstp:) or mms (mms:) server. The URL of the song to play is specified by ``MediaUrl`` parameter.

.. note::

	``play_web_media`` does not specify speakers to play. It just uses the current session setting. If there is no speaker in the current session, then the play fails.
	
.. note::

	``play_web_media`` cannot be resumed. If it is paused by calling ``pause``, then it just stops playing music, and cannot resume.
	
	
- API: GET /v1/play_web_media?SessionID=<session id>&MediaUrl=<URL of the song>
- Response
	- Play a song from HTTP server, and then return true or false.
- Example:
	- Request: ``http://192.168.1.10:8080/v1/play_web_media?SessionID=1000&MediaUrl=http://seonman.github.io/music/hyolyn.mp3``
	- Response: 

.. code-block:: json

	{"Result":"true"}

.. Note::
	This API call takes several hundreds millisecond to return the response.
	
----

Play a Song from Web Server as party mode
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Plays a song from Web server with all speakers. The URL of the song to play is specified by ``MediaUrl`` parameter.

.. note::

	``play_web_media`` cannot be resumed. If it is paused by calling ``pause``, then it just stops playing music, and cannot resume.
	

- API: GET /v1/play_web_media_party_mode?SessionID=<session id>&MediaUrl=<URL of the song>
- Response
	- Play a song from HTTP server to all speakers, and then return true or false.
- Example:
	- Request: ``http://192.168.1.10:8080/v1/play_web_media_party_mode?SessionID=1000&MediaUrl=http://seonman.github.io/music/hyolyn.mp3``
	- Response: 

.. code-block:: json

	{"Result":"true"}

.. Note::
	This API call takes several hundreds millisecond to return the response.
	
----

Play a Song from Web Server with selected speakers
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Plays a song from Web server with selected speakers. The URL of the song to play is specified by ``MediaUrl`` parameter. The selected speakers are represented in ``DeviceIDList`` parameter as a list of ``DeviceID`` separated by ",".

.. note::

	``play_web_media`` cannot be resumed. If it is paused by calling ``pause``, then it just stops playing music, and cannot resume.

- API: GET /v1/play_web_media_selected_speakers?SessionID=<session id>&MediaUrl=<URL of the song>&DeviceIDList=<xxx,xxx,...>
- Response
	- Play a song from HTTP server to selected speakers, and then return true or false.
- Example:
	- Request: ``http://192.168.1.10:8080/v1/play_web_media_selected_speakers?SessionID=1000&MediaUrl=http://seonman.github.io/music/hyolyn.mp3&DeviceIDList=34317244381360,129321920968880""``
	- Response: 

.. code-block:: json

	{"Result":"true"}

.. Note::
	This API call takes several hundreds millisecond to return the response.
	
----

Pause the Current Playback
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

- API: GET /v1/pause_play?SessionID=<session id>
- Response
	- Pause the current playback, and then return true or false.
	- It can resume the current playback by calling ``resume_hub_media`` if and only if the playback is playing hub media. ``play_web_media`` cannot be resumed once it is paused or stopped.
- Example:
	- Request: ``http://192.168.1.10:8080/v1/pause_play?SessionID=1000``
	- Response: 

.. code-block:: json

	{"Result":"true"}
	
----

Resume the Current Playback with Hub Media
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

- API: GET /v1/resume_hub_media?SessionID=<session id>&PersistentID=<persistent id>
- Response
	- Resume the current playback with Hub Media, and then return true or false.
- Example:
	- Request: ``http://192.168.1.10:8080/v1/resume_hub_media?SessionID=1000&PersistentID=7387446959931482519``
	- Response: 

.. code-block:: json

	{"Result":"true"}

----

Resume the Current Playback with Hub Media as Party Mode
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

- API: GET /v1/resume_hub_media_party_mode?SessionID=<session id>&PersistentID=<persistent id>
- Response
	- Resume the current playback with Hub Media with all speakers, and then return true or false.
- Example:
	- Request: ``http://192.168.1.10:8080/v1/resume_hub_media_party_mode?SessionID=1000&PersistentID=7387446959931482519``
	- Response: 

.. code-block:: json

	{"Result":"true"}

----

Resume the Current Playback with Hub Media with selected speakers
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

- API: GET /v1/resume_hub_media_selected_speakers?SessionID=<session id>&PersistentID=<persistent id>&DeviceIDList=<xxx,xxx,...>
- Response
	- Resume the current playback with Hub Media with selected speakers, and then return true or false.
- Example:
	- Request: ``http://192.168.1.10:8080/v1/resume_hub_media_selected_speakers?SessionID=1000&PersistentID=7387446959931482519&DeviceIDList=34317244381360,129321920968880``
	- Response: 

.. code-block:: json

	{"Result":"true"}

----

Stop the Current Playback
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

- API: GET /v1/stop_play?SessionID=<session id>
- Response
	- Stop the current playback with Hub Media, and then return true or false.
	- If the playback has stopped, then it cannot resume.
- Example:
	- Request: ``http://192.168.1.10:8080/v1/stop_play?SessionID=1000``
	- Response: 

.. code-block:: json

	{"Result":"true"}
	
----

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

	- Note that if the playback has stopped, then it cannot resume.
	- Developers need to check the playback status during the playback to handle any possible exceptional cases like interruption or errors. We recommedn to call this API every second.
- Example:
	- Request: ``http://192.168.1.10:8080/v1/playback_status?SessionID=1000``
	- Response: 

.. code-block:: json

	{"PlaybackState":"PlayerStatePlaying",
	 "TimeElapsed":"15"}

----

Check if the Hub is playing audio
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

- API: GET /v1/is_playing?SessionID=<session id>
- Response
	- Returns true (playing) or false (not playing)
- Example:
	- Request: ``http://192.168.1.10:8080/v1/is_playing?SessionID=1000``
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
	- Request: ``http://192.168.1.10:8080/v1/get_volume?SessionID=1000``
	- Response: 

.. code-block:: json

	{"Volume":"10"}

----

Get Volume for a particular device
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

- API: GET /v1/get_volume_device?SessionID=<session id>&DeviceID=<device id>
- Response
	- Returns the  volume of a particular device
	- The range of volume is 0 (muted) to 50 (max)
- Example:
	- Request: ``http://192.168.1.10:8080/v1/get_volume_device?SessionID=1000&DeviceID=1234567``
	- Response: 

.. code-block:: json

	{"Volume":"10"}

----

Set Volume for all devices
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

- API: GET /v1/set_volume?SessionID=<session id>&Volume=<volume>
- Response
	- Returns true or false
- Example:
	- Request: ``http://192.168.1.10:8080/v1/set_volume?SessionID=1000&Volume=10``
	- Response: 

.. code-block:: json

	{"Result":"true"}
	
----

Set Volume for a particular device
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

- API: GET /v1/set_volume_device?SessionID=<session id>&DeviceID=<device id>&Volume=<volume>
- Response
	- Returns true or false
- Example:
	- Request: ``http://192.168.1.10:8080/v1/set_volume_device?SessionID=1000&DeviceID=1234567&Volume=10``
	- Response: 

.. code-block:: json

	{"Result":"true"}