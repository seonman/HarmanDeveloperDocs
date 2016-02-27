HKWHub App - Connecting your Omni speakers to any 3rd party services or devices (iOS)
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
	- All the 3rd party apps or services should be authorized with OAuth2 to get access token. An access token is required when 3rd party apps call the REST APIs. The detailed information about OAuth2 is available at `this link`_.
	
.. _this link: http://harmandeveloperdocs.readthedocs.org/en/latest/iOS/hkwhub-spec.html#id2

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

.. Note::

	You can download the HKWHub app from App Store (`click here`_)
	
.. _click here: https://itunes.apple.com/us/app/hk-whub-app/id1057062847?ls=1&mt=8

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

----


HKIoTCloud Mode
~~~~~~~~~~~~~~~~~~~

Connecting to HKIoTCloud
^^^^^^^^^^^^^^^^^^^^^^^^^^^

In HKIoTCloud demo, 3rd party clients can connect to HKIoTCloud (http://hkiotcloud.herokuapp.com) and send REST requests to control speakers and play audio. In order to use HKIoTCloud mode, user needs to sign up to the cloud with username, emaill address and password. Once sign up is done, user need to sign in to the server. User sign-up and sign-in can be done within the HKWHub app, as shown below.
	
.. figure:: img/hub/hubappv2-signin.jpg
	:scale: 30

Once the HKWHub app successfully signs in to HKIoTCloud, the screen will be switched to Log screen, like shown as below. You can see all the message logs received from or sent to the cloud. Each log contains a JSON data, so you can see what information is being sent and received between the server. 

.. figure:: img/hub/hubappv2-afterlogin.jpg
	:scale: 30
	
If you want to disconnect the server and return to the main screen, press **Disconnect** button on the top righthand corner.

Sending REST Requests to HKIoTCloud
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Once the HKWHub App is running, you can now connect a client to HKIoTCloud and send REST requests to the server. We will explain about the REST APIs supported with a little more detailed example of **curl** commands in the next section.

.. Note::
		
	For a client to connect to HKIoTCloud, the same username and password are required. 

As an example of client, HKIoTCloud hosts a Web-based client app, at http://hkiotcloud.herokuapp.com/webapp/. The following is a screenshot of the web app.

.. figure:: img/hub/cloudapp-login.png
	:scale: 70

Once user authentication is done successfully, the Web app will switch the screen to the Playlist screen.

.. figure:: img/hub/cloudapp-medialist.png
	:scale: 70

Now, you can click one of the titles in the list, and see how the web app is playing the title, showing the information of the title, volume, and playback time, and so on.

.. figure:: img/hub/cloudapp-mediaplayer.png
	:scale: 70

If you click **Speaker List** menu on the left, you can see more detailed information of speakers like below, and can control speakers, like remove a speaker from the current playback session or add a speaker to playback. 

.. figure:: img/hub/cloudapp-speakers.png
	:scale: 70
	


Local Server Mode
~~~~~~~~~~~~~~~~~~~

Running Local Server
^^^^^^^^^^^^^^^^^^^^^^^^

Loca Server Mode is almost the same as HKIoTCloud, except that HKWHub app runs a web server inside, instead connecting to HKIoTCloud. Therefore, HKWHub app can receive REST requests directly from clients in the same network. If you want to connect speakers from any type of devices in the same local network, then Local Server mode can be easier solution.

Once you click **Run Local Web Server** menu, then you will see the following screen. From the screen, you can see a URL indicating where a client should connect to. In this example, the client should enter the URL **http://10.0.1.37:8080/**  followed by REST command and parameters.

The RESI APIs are almost the same as the ones of HKIoTCloud mode.

.. figure:: img/hub/hubappv2-localserver.jpg
	:scale: 30


Sending REST Requests to LocalServer
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

As a sample client app, you can use **WebHubWebApp** that you can download from Harman Developer web site (http://developer.harman.com) or direclty from :download:`here <download/WebHubWebApp.zip>`. The Web app is created using Polymer v0.5 (https://www.polymer-project.org/0.5/).

Once you download the app, unzip it. You will see the following sub directories.

- bower_components: THis is the folder where polymer libraries are located.
- hkwhub: this is the folder containing the WebHubApp source code.

.. code-block:: shell

	$ cd WebHubWebApp
	$ python -m SimpleHTTPServer
	
You will get some log messages like "Serving HTTP on 0.0.0.0 port 8000 ..."

Next, launch your web browser (Chrome, Safari, ...) and go to http://localhost:8000/hkwhub/

.. note::

	Your iOS device running HKWHub app and your Desktop PC running web browser should be in the same network.

At the fist screen looking like this:

.. figure:: img/hub/webapp-initial.png
	:scale: 70

Enter the URL that the HKWHub app says: http://10.0.1.37:8080/, like this:

.. figure:: img/hub/webapp-initial-url.png
	:scale: 70

If you press **Submit**, then you will see the first screen like below. This is the list of media items available at the HKWHub app. 

.. figure:: img/hub/webapp-afterlogin.png
	:scale: 70
	
The UI of the Web app is exactly the same as HKIoTCloud web app. So, we skip to explain the rest parts of the app.


PubNub Server Mode
~~~~~~~~~~~~~~~~~~~

Connect to PubNub Server
^^^^^^^^^^^^^^^^^^^^^^^^^^^

With PubNub server mode, any PubNub client can connect to and control Omni speakes managed by HKWHub app. Just click **Connect to PubNub Cloud** menu in the main screen, then you will see the screen like below. Please check if the logs are saysing something like "Received: Hello from HKWHubApp" which is the message sent back from PubNub server after the HKWHub app published the message. This means the app is now connected to PubNub cloud.

.. figure:: img/hub/hubappv2-pubnub.jpg
	:scale: 30

Differently from HKIoTCloud or Local Server mode that relies on **REST API** for control and playback of speakers, PubNub is using Publish/Subscribe messaging instead. And in order to route the message among clients, we should set **PubNub Channel** so that all the published messages are correctly routed to subscribed clients of the same channel.

So, for HKWHub app successfully connects to PubNub cloud, user needs to set PubNub **Publish Key**, **Subscribe Key**, and **Channel**. As explained already, user can set these keys in the **Settings/Set API Keys** menu in the main screen.


Sending REST Requests to PubNub Cloud
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Once the HKWHub app is connected to PubNub cloud, a PubNub client can send PubNub message. Even though it does not use REST API, but use PubNub's Subscribe/Publish messaging instead, the content of the messages are almost the same as the REST APIs, and it is in JSON format.

.. Note::
		
	One biggest difference between REST API and Publish/Subscribe messaging is that Pub/Sub messaging does not need to do **Polling** for getting information from the server when an event occurs on the server side, because REST API does not support **callback** mechanism to notify an **event** to clients. However, Pub/Sub messaging is bidirectional, the client can get notified immediately from the server. Either client or server can publish a message to the channel being shared to notify an event to subsribers.
	
In this reason, the messages of request and response for speaker control are a littke different. For a client to send a command to speaker, the client **publish** the command to the channel. Then because HKWHub app is one of the clients, it receives the command, and process the command internally. If the command requires a response, then HKWHub app should send the response back to the client. To to that, HKWHub app also needs to **publish** the response to the channel. And, the client will get the response because it subscribed to the channel.

If HKWHub app has some event to report to notify to clients, for example, device status changed, or playback time changed, etc., then HKWHub app publish the events to the channel, then all the client listening to the channel will receive the event.

Sample Web App
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

As a sample client app, you can use **WebHubPubNubApp** that you can download from Harman Developer web site (http://developer.harman.com) or directly from :download:`here <download/WebHubPubNubApp.zip>`.. Likewise, The Web app is created using Polymer v0.5 (https://www.polymer-project.org/0.5/).

Once you download the app, unzip it. You will see the following sub directories.

- bower_components: THis is the folder where polymer libraries are located.
- hkwhub: this is the folder containing the WebHubApp source code.

.. code-block:: shell

	$ cd WebHubPubNubApp
	$ python -m SimpleHTTPServer
	
You will get some log messages like "Serving HTTP on 0.0.0.0 port 8000 ..."

Next, launch your web browser (Chrome, Safari, ...) and go to http://localhost:8000/hkwhub/

.. note::

	Your iOS device running HKWHub app and your Desktop PC running web browser should be in the same network.

At the fist screen looking like below. Note that it looks different from the screen from Local Server mode, which requires only URL of the web server.

.. figure:: img/hub/pubnubapp-login.png
	:scale: 70

Enter the same PubNub publish key, subscribe key, and channel name that you used for HKWHub app, and click **Submit**, as below.

https://chrome.google.com/webstore/detail/postman/fhbjgbiflinjbdggehcddcbncdddomop/related?hl=en

If you press **Submit**, then you will see the first screen like below. This is the list of media items available at the HKWHub app. 

.. figure:: img/hub/pubnubapp-medialist.png
	:scale: 70
	
The UI of the Web app is exactly the same as HKIoTCloud web app. So, we skip to explain the rest parts of the app.


Use ``curl`` command to send REST requests
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

We show how to control Omni speakers by sending REST requests to HKIoTCloud. Sending REST requests to Local Server is almost the same. 

You can use **curl** command in your shell to send REST requests.

If you are a chrome browser user, you can use **Postman** (https://chrome.google.com/webstore/detail/postman/fhbjgbiflinjbdggehcddcbncdddomop/related?hl=en) chrome extension to send HTTP requests with browser-based UI.


.. figure:: img/hub/postman.png
	:scale: 70
	
.. Note::

	Before you do this, do not forget to run HKWHub App and connect to HKIoTCloud.
	
Get an Access Token and Refresh Token (HKIoTCloud mode only)
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

In case of HKIoTCloud more, the client should get an access token from the HKIoTCloud to be able to call the REST APIs. HKIoTCloud supports two authorization modes: **password** and **authorization code**. For mode detailed information, please refer to the section of `OAuth2 Authorization API Specification`_.

.. _OAuth2 Authorization API Specification: http://harmandeveloperdocs.readthedocs.org/en/latest/iOS/hkwhub-spec.html#id2

With **password** grant mode, you can get an access token and a refresh token as shown below:


- curl -X POST -H "Authorization: Basic bjdIaGlUbktZakpkNHptTTpBTlJmQjl6OTR4dGN4RkdYcmQ1WEhYRWlLZzQzVVk=" -d "grant_type=password&username=yyy&password=xxx" http://hkiotcloud.herokuapp.com/oauth/token

Result:

.. code::

	{"token_type":"bearer",
	 "access_token":"15c0507f3a550d7a31f7af5dc45e4dd9fd9f4bc8",
	 "expires_in":3600,
	 "refresh_token":"1b470edc539681803de95c919bc3779acdf34e01"}


When you call the HKIoTCloud API calls, you should pass the value of the access token into the request header. Specifically, create an ``Authorization`` header and give it the value ``Bearer <access token>``.

a. Init session
^^^^^^^^^^^^^^^

- curl -H "Authorization: Bearer 15c0507f3a550d7a31f7af5dc45e4dd9fd9f4bc8" http://hkiotcloud.herokuapp.com/api/v1/init_session

This returns the SessionToken. The returned SessionToken is used by all subsequent REST API request in the body.

.. code:: json

	{"ResponseOf":"init_session","SessionToken":"r:abciKaTbUgdpQFuvYtgMm0FRh"}


b. Add alls speaker to session
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

After HKWHub app is launched, none of speakers is selected for playback. You need to add one or more speakers to play audio. To add all speakers to playback session, use ``set_party_mode``. **Party Mode** is the mode where all speakers are playing the same audio together with synchronization. So, by ``set_party_mode``, you can select all speakers to play.


- curl -H "Authorization: Bearer 15c0507f3a550d7a31f7af5dc45e4dd9fd9f4bc8" "http://hkiotcloud.herokuapp.com/api/v1/set_party_mode?SessionToken=r:abciKaTbUgdpQFuvYtgMm0FRh"
	
.. code:: json

	{"Result":"true","ResponseOf":"set_party_mode"}

c. Get the list of speakers available
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
To control speakers individually, you can get the list of speakers available by using **device_list** command.

- curl -H "Authorization: Bearer 15c0507f3a550d7a31f7af5dc45e4dd9fd9f4bc8" "http://hkiotcloud.herokuapp.com/api/v1/device_list?SessionToken=r:abciKaTbUgdpQFuvYtgMm0FRh"

.. code:: json

	{"DeviceList":[
		{
			"IsPlaying":false,
			"MacAddress":"",
			"GroupName":"Garage",
			"Role":21,
			"Version":"0.1.6.2",
			"Port":44055,
			"Active":true,
			"GroupID":"4625984469",
			"ModelName":"Omni Adapt",
			"DeviceID":"4625984469168",
			"IPAddress":"10.0.1.6",
			"Volume":17,
			"DeviceName":"Adapt",
			"WifiSignalStrength":-62
		},
		{
			"IsPlaying":false,
			"MacAddress":"b0:38:29:11:19:54",
			"GroupName":"Living Room",
			"Role":21,
			"Version":"0.1.6.2",
			"Port":44055,
			"Active":true,
			"GroupID":"9246663882",
			"ModelName":"Omni 10",
			"DeviceID":"92466638829744",
			"IPAddress":"10.0.1.9",
			"Volume":17,
			"DeviceName":"Omni Left",
			"WifiSignalStrength":-67
		}
	],
	"ResponseOf":"device_list"
	}

	
d. Add a speaker to session
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

If you want to add a speaker to session, use ``add_device_to_session`. It requires ``DeviceID`` parameter to identify a speaker to add. This command does not impact other speakers regardless of their status.

- curl -H "Authorization: Bearer 15c0507f3a550d7a31f7af5dc45e4dd9fd9f4bc8" "http://hkiotcloud.herokuapp.com/api/v1/add_device_to_session?SessionToken=r:abciKaTbUgdpQFuvYtgMm0FRh&DeviceID=4625984469168"

.. code:: json

	{"Result":"true","ResponseOf":"add_device_to_session"}

e. Get the media list
^^^^^^^^^^^^^^^^^^^^^^^

- curl -H "Authorization: Bearer 15c0507f3a550d7a31f7af5dc45e4dd9fd9f4bc8" "http://hkiotcloud.herokuapp.com/api/v1/media_list?SessionToken=r:abciKaTbUgdpQFuvYtgMm0FRh"

Here, SessionToken should be the session token you got from ``init_session``. You will get a list of media in JSON like below

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

f. Play a media item listed in the HKWHub app
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
If you want to play a media item listed in the HKWHub app, use ``play_hub_media`` by specifying the media item with ``PersistentID``. The ``PersistentID`` is available from the response of ``media_list`` command.

.. note::

	Note that, before calling ``play_hub_media``, at least one or more speakers must be selected (added to session) in advance. If not, then the playback will fail. 

- curl -H "Authorization: Bearer 15c0507f3a550d7a31f7af5dc45e4dd9fd9f4bc8" "http://hkiotcloud.herokuapp.com/api/v1/play_hub_media?SessionToken=r:abciKaTbUgdpQFuvYtgMm0FRh&PersistentID=1062764963669236741"

.. code-block:: json

	{"Result":"true","ResponseOf":"play_hub_media"}


f. Play a media item in the HKWHub by specifying a speaker list to play
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
You can play a media item in the HKWHub app by specifying the list of speakers.

- curl -H "Authorization: Bearer 15c0507f3a550d7a31f7af5dc45e4dd9fd9f4bc8" "http://hkiotcloud.herokuapp.com/api/v1/play_hub_media_selected_speakers?SessionToken=r:abciKaTbUgdpQFuvYtgMm0FRh&PersistentID=1062764963669236741&DeviceIDList=34317244381360,129321920968880"

The list of speakers are listed by the parameter ``DeviceIDList`` with delimitor ",".

.. code-block:: json

	{"Result":"true","ResponseOf":"play_hub_media_selected_speakers"}

g. Play a HTTP streaming media as party mode
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

- curl -H "Authorization: Bearer 15c0507f3a550d7a31f7af5dc45e4dd9fd9f4bc8" "http://hkiotcloud.herokuapp.com/api/v1/play_web_media_party_mode?SessionToken=r:abciKaTbUgdpQFuvYtgMm0FRh&MediaUrl=http://seonman.github.io/music/hyolyn.mp3"

.. code-block:: json

	{"Result":"true","ResponseOf":"play_web_media_party_mode"}

h. Stop playing
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

- curl -H "Authorization: Bearer 15c0507f3a550d7a31f7af5dc45e4dd9fd9f4bc8" "http://hkiotcloud.herokuapp.com/api/v1/stop_play?SessionToken=r:abciKaTbUgdpQFuvYtgMm0FRh"

.. code-block:: json

	{"Result":"true","ResponseOf":"stop_play"}

i. Set Volume
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

- curl -H "Authorization: Bearer 15c0507f3a550d7a31f7af5dc45e4dd9fd9f4bc8" "http://hkiotcloud.herokuapp.com/api/v1/set_volume?SessionToken=r:abciKaTbUgdpQFuvYtgMm0FRh&Volume=30"

.. code-block:: json

	{"Result":"true","ResponseOf":"set_volume"}

.. note::

	Please see the REST API specification for more information and examples.


Playback Session Management
-----------------------------

Since the HKWHub app should be able to handle REST HTTP requests from more than one clients at the same time, the HKWHub app manages the requests with session information associated with the priority when a new playback is initiated.

The following is the policy of the session management:

Playback Session Creation
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
- When a client wants to start a playback, it sets the priority of the session (using ``Priority=<priority value>`` parameter).
- If Priority parameter is not specified, HKWHub app assumes it as default value, that is, 100.

Priority of Session
~~~~~~~~~~~~~~~~~~~~~
- Each session is associated with a priority value which will be used to determine which request can override the current on-going playback session.
- The priority value is specified as parameter (``Priority``) when the client calls ``play_xxx``.
	- If the command does not specify the Priority parameter, 100 is set as default value.
- If the priority of a new playback request, such as ``play_hub_media`` or ``play_web_media``, and so on, is greater than or equal to the priority of the current playback session, then it interrupts the current playback session, that is, stops the current playback session and start a new playback for itself.
	- The playback status of the interrupted session becomes ``PlayerStateStopped``. (see the related API in the next section)
	
The following diagrams show how HKWHub app handles incoming playback request based on the session priorities.

.. figure:: img/hub/session-management.png
	:alt: Session management flow diagram

Session Timeout
~~~~~~~~~~~~~~~~~
- A session becomes expired and invalid when about 60 minutes is passed since the last command was received.
- Session timer is extended (renewed) once a playback is executed successfully.
- All requests with expired session will be denied and "SessionNotFound" error returns.



----

REST API Specification (including PubNub JSON format)
-------------------------------------------------------

This specification describes about the REST APIs to control HK Omni speakers and stream audio to the speakers via HKWHub app.

All the APIS are in REST API protocol.

.. Note::
	
	In this documentation, for HKIoTCloud mode, <server_host> should be "hkiotcloud.herokuapp.com".
	For Local server mode, <server_host> should be the URL (IP address and port number) tat HKWHub app is showing.

.. Note::

	PubNub server mode does not use REST API. Instead, PubNub client needs to subscribe to the PubNub channel to get events from HKWHub, and use publish message to the PubNub channel to send request to HKWHub. The commands and parameters of each command are the same as REST API specification. However, PubNub message needs to include a couple of additional parameters in the JSON data to specify the **HKWHub UUID (HKWHubUUID)** that are talking to. The response message coming from the HKWHub app will include **ResponseOf** parameters to specify which request the resonse was for.
	
	So, we will describe PubNub message specification along with REST API specification here.


.. Note::

	All the REST request should contain ``Authorization`` header that contains the access token, as described above.


Session Management
~~~~~~~~~~~~~~~~~~~~

Start Session
^^^^^^^^^^^^^^
This starts a new session. As a response, the client will receive a SessionToken. The SessionToken is required to be sent in any following requests. Note that the REST requests differs depending on the server mode.


- API: GET /api/v1/init_session
- Response
	- Returns a unique session token
	- The session token will be used for upcoming requests.
- Example:
	- Request: 
	
	.. code-block:: json
	
		curl -H "Authorization: Bearer 15c0507f3a550d7a31f7af5dc45e4dd9fd9f4bc8" http://<server_host>/api/v1/init_session

	- Response: 

	.. code-block:: json

		{"ResponseOf":"init_session","SessionToken":"r:abciKaTbUgdpQFuvYtgMm0FRh"}

- PubNub
	- Publish Message

	.. code-block:: json

		{Command = "init_session"}

	- Message from HKWHub (via Subscribe)
		- Note that the response of ``init_session`` will contain **HKWHubUUID** to identify the HKWHub the PubNub client is getting talking to. The subsequent Publish message should include this HKWHubUUID information as well as SessionToken.

	.. code-block:: json

		{
		HKWHubUUID = "XXX-XXX-XXX-XXX",
		SessionToken = "PubNub-1000",
		ResponseOf = "init_session"
		}
			
----

Close Session
^^^^^^^^^^^^^^
Close the session. The SessionToken information is removed from the session table.

- API: GET /api/v1/close_session?SessionToken=<session token>
- Response
	- Returns true or false indicating success or failure
- Example:
	- Request:
	
	.. code-block:: json	
	
		http://<server_host>/api/v1/close_session?SessionToken=r:abciKaTbUgdpQFuvYtgMm0FRh
		
	- Response: 

	.. code-block:: json

		{"Result" : "true"}

- PubNub
	- Publish Message

	.. code-block:: json

		{
		HKWHubUUID = "XXX-XXX-XXX-XXX",
		SessionToken = "PubNub-1000",
		Command = "close_session"
		}

	- Message from HKWHub (via Subscribe)

	.. code-block:: json

		{
		HKWHubUUID = "XXX-XXX-XXX-XXX",
		SessionToken = "PubNub-1000",
		ResponseOf = "close_session",
		Result = true
		}
		
----

Device Management
~~~~~~~~~~~~~~~~~~~~

Get the device count
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Returns the number of speakers available in the network.

- API: GET /api/v1/device_count?SessionToken=<session token>
- Response
	- Returns the number of devices connected to the network
- Example:
	- Request: 
	
	.. code-block:: json
	
		http://<server_host>/api/v1/device_count?SessionToken=r:abciKaTbUgdpQFuvYtgMm0FRh
		
	- Response: 

	.. code-block:: json

		{"DeviceCount":"2"}

- PubNub
	- Publish Message

	.. code-block:: json

		{
		HKWHubUUID = "XXX-XXX-XXX-XXX",
		SessionToken = "PubNub-1000",
		Command = "device_count"
		}

	- Message from HKWHub (via Subscribe)

	.. code-block:: json

		{
		HKWHubUUID = "XXX-XXX-XXX-XXX",
		SessionToken = "PubNub-1000",
		ResponseOf = "device_count",
		DeviceCount = 2
		}
		
		
----


Get the list of devices and their information
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Returns the list of speakers and their information including several status information.

- API: GET /api/v1/device_list?SessionToken=<session token>
- Response
	- Returns the list of devices with all the device information
- Example:
	- Request: 
	
	.. code-block:: json	
	
		http://<server_host>/api/v1/device_list?SessionToken=r:abciKaTbUgdpQFuvYtgMm0FRh
	
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
		
- PubNub
	- Publish message
	
	.. code-block:: json	
	
		{
		Command = "device_list",
		HKWHubUUID = "XXX-XXX-XXX-XXX",
		SessionToken = "PubNub-1000"
		}
		
	- Response message (from Subscribed)
	
	.. code-block:: json	
	
 .. code-block:: json

 	   {
	   HKWHubUUID = "XXX-XXX-XXX-XXX",
	   SessionToken = "PubNub-1000",
	   ResponseOf = "device_list",
	   DeviceList = 
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
Gets the device information of a particular device (speaker) identified by DeviceID.

- API: GET /api/v1/device_info?SessionToken=<session token>&DeviceID=<device id>
- Response
	- Returns the information of the device
- Example:
	- Request:
	
	.. code-block:: json
	
		http://<server_host>/api/v1/device_info?SessionToken=r:abciKaTbUgdpQFuvYtgMm0FRh&DeviceID=129321920968880

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

- PubNub
	- Publish message
	
	.. code-block:: json	
	
		{
		Command = "device_list",
		HKWHubUUID = "XXX-XXX-XXX-XXX",
		SessionToken = "PubNub-1000"
		}
		
	- Response message (from Subscribed)
	
	.. code-block:: json	
	
 	   {
	   HKWHubUUID = "XXX-XXX-XXX-XXX",
	   SessionToken = "PubNub-1000",
	   ResponseOf = "device_list",
	   GroupName: "Temp", 
	   Role = 21, 
	   MacAddress = "b0:38:29:1b:9e:75", 
	   WifiSignalStrength = -52, 
	   Port = 44055, 
	   Active = true, 
	   DeviceName = "Adapt", 
	   Version = "0.1.6.2", 
	   ModelName = Omni Adapt", 
	   IPAddress = "192.168.1.39", 
	   GroupID = 1293219209", 
	   Volume = 47, 
	   IsPlaying = true, 
	   DeviceID = 129321920968880"
		}
	
----

Add a Device to Session
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Add a speaker to playback session. Once a speaker is added, then the speaker will play the music. There is no impact of this call to other speakers.

- API: GET /api/v1/add_device_to_session?SessionToken=<session token>&DeviceID=<device id>
- Response
	- Returns true or false
- Example:
	- Request:
	
	.. code-block:: json
	
		http://<server_host>/api/v1/add_device_to_session?SessionToken=r:abciKaTbUgdpQFuvYtgMm0FRh&DeviceID=129321920968880

	- Response: 

	.. code-block:: json

		{"Result":"true"}

- PubNub
	- Publish message
	
	.. code-block:: json
	
		{
		HKWHubUUID = "XXX-XXX-XXX-XXX",
		SessionToken = "PubNub-1000",
		Command = "add_device_to_session",
		DeviceID = "129321920968880"
		}

		
	- Response: 
	
	.. code-block:: json

		{
		HKWHubUUID = "XXX-XXX-XXX-XXX",
		SessionToken = "PubNub-1000",
		ResponseOf = "add_device_to_session",
		Result = true
		}
	
----

Remove a Device from Session
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Removes a speaker from playback session. Once a speaker is removed, then the speaker will not play the music. There is no impact of this call to other speakers.

- API: GET /api/v1/remove_device_from_session?SessionToken=<session token>&DeviceID=<device id>
- Response
	- Returns true or false
- Example:
	- Request: 
	
	.. code-block:: json
	
		http://<server_host>/api/v1/remove_device_from_session?SessionToken=r:abciKaTbUgdpQFuvYtgMm0FRh&DeviceID=129321920968880
		
	- Response: 
	
	.. code-block:: json

		{"Result":"true"}
		
- PubNub
	- Publish message
	
	.. code-block:: json
	
		{
		HKWHubUUID = "XXX-XXX-XXX-XXX",
		SessionToken = "PubNub-1000",
		Command = "remove_device_from_session",
		DeviceID = "129321920968880"
		}

		
	- Response: 
	
	.. code-block:: json

		{
		HKWHubUUID = "XXX-XXX-XXX-XXX",
		SessionToken = "PubNub-1000",
		ResponseOf = "remove_device_from_session",
		Result = true
		}
	

Set party mode
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Addes all speakers to playback session. Once it is done, all speakers will play music.

- API: GET /api/v1/set_party_mode?SessionToken=<session token>
- Response
	- Returns true or false
- Example:
	- Request: 
	
	.. code-block:: json
	
		http://<server_host>/api/v1/set_party_mode?SessionToken=r:abciKaTbUgdpQFuvYtgMm0F

	- Response: 
	
	.. code-block:: json

		{"Result":"true"}

			
- PubNub
	- Publish Message

	.. code-block:: json

		{
		HKWHubUUID = "XXX-XXX-XXX-XXX",
		SesssionToken = "PubNub-1000",
		Command = "set_party_mode"
		}

	- Message from HKWHub (via Subscribe)

	.. code-block:: json

		{
		HKWHubUUID = "XXX-XXX-XXX-XXX",
		ResponseOf = "set_party_mode",
		Result = true
		}
		
----

Media Playback Management
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Get the list of media item in the Media List of the HKWHub app
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Returns the list of media items added to the Media List of the app. User can add music items to the **Media List** of the app via **Setting** of the app.

.. Note::

	A music item downloaded from Apple Music is not supported. The music file from Apple music is DRM-enabled, and cannot be played with HKWirelessHD. Only music items purchased from iTunes Music or added from user's own library are supported.

	To be added to the Media List, the music item must be located locally on the device. No streaming from iTunes or Apple Music are supported.


- API: GET /api/v1/media_list?SessionToken=<session token>
- Response
	- Returns JSON of the list of store media in the HKWHub app.
- Example:
	- Request: 
	
	.. code-block:: json
	
		http://<server_host>/api/v1/media_list?SessionToken=r:abciKaTbUgdpQFuvYtgMm0F
		
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
			
- PubNub
	- Publish Message

	.. code-block:: json

		{Command = "media_list",
		 HKWHubUUID = "XXX-XXX-XXX-XXX",
		 SesssionToken = "PubNub-1000"}

	- Message from HKWHub (via Subscribe)

	.. code-block:: json

		{
		HKWHubUUID = "XXX-XXX-XXX-XXX",
		MediaList = [
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
			],
		ResponseOf = "media_list"
		}
	
----

Play a song in the Media List of the HKWHub app
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Plays a song in the Media List of the Hub app. Each music item is identified with MPMediaItem's PersistentID. It is a unique ID to identify a song in the iOS Music library.

.. note::

	``play_hub_media`` does not specify speakers to play. It just uses the current session setting. If there is no speaker in the current session, then the play fails.

- API: GET /api/v1/play_hub_media?SessionToken=<session token>&PersistentID=<persistent id>
- Response
	- Play a song stored in the hub, and then return true or false.
- Example:
	- Request:
	
	.. code-block:: json
	
		http://<server_host>/api/v1/play_hub_media?SessionToken=r:abciKaTbUgdpQFuvYtgMm0F&PersistentID=7387446959931482519

	- Response: 

	.. code-block:: json

		{"Result":"true"}

- PubNub
	- Publish Message

	.. code-block:: json

		{
		HKWHubUUID = "XXX-XXX-XXX-XXX",
		Command = "play_hub_media",
		PersistentID = 7387446959931482519,
		SesssionToken = "PubNub-1000"
		}

	- Message from HKWHub (via Subscribe)

	.. code-block:: json

		{
		HKWHubUUID = "XXX-XXX-XXX-XXX",
		SessionToken = "PubNub-1000",
		ResponseOf = "play_hub_media",
		Result = true
		}
		
----

Play a song in the Media list as party mode
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Plays a song in the Media List with all speakers available. So, regardless of current session setting, this command play a song to all speakers.

- API: GET /api/v1/play_hub_media_party_mode?SessionToken=<session token>&PersistentID=<persistent id>
- Response
	- Play a song in the hub's media list to all speakers, and then return true or false.
- Example:
	- Request:
	
	.. code-block:: json 
		
		http://<server_host>/api/v1/play_hub_media_party_mode?SessionToken=r:abciKaTbUgdpQFuvYtgMm0F&PersistentID=7387446959931482519
		
	- Response: 

	.. code-block:: json

		{"Result":"true"}
		
- PubNub
	- Publish Message

	.. code-block:: json

		{
		Command = "play_hub_media_party_mode",
		HKWHubUUID = "XXX-XXX-XXX-XXX",
		PersistentID = 7387446959931482519,
		SesssionToken = "PubNub-1000"
		}

	- Message from HKWHub (via Subscribe)

	.. code-block:: json

		{
		HKWHubUUID = "XXX-XXX-XXX-XXX",
		SessionToken = "PubNub-1000",
		ResponseOf = "play_hub_media_party_mode",
		Result = true
		}

----

Play a song in the Media list with selected speakers
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Plays a song in the Media List with selected speakers. The selected speakers are represented in ``DeviceIDList`` parameter as a list of ``DeviceID`` separated by ",".

- API: GET /api/v1/play_hub_media_selected_speakers?SessionToken=<session token>&PersistentID=<persistent id>&DeviceIDList=<xxx,xxx,...>
- Response
	- Play a song in the hub's media list to selected speakers, and then return true or false.
- Example:
	- Request:
	
	.. code-block:: json
	
		http://<server_host>/api/v1/play_hub_media_selected_speakers?SessionToken=r:abciKaTbUgdpQFuvYtgMm0F&PersistentID=7387446959931482519&DeviceIDList=34317244381360,129321920968880

	- Response: 

	.. code-block:: json

		{"Result":"true"}

- PubNub
	- Publish Message

	.. code-block:: json

		{
		Command = "play_hub_media_selected_speakers",
		HKWHubUUID = "XXX-XXX-XXX-XXX",
		PersistentID = 7387446959931482519,
		SesssionToken = "PubNub-1000",
		DeviceIDList = 34317244381360,129321920968880
		}

	- Message from HKWHub (via Subscribe)

	.. code-block:: json

		{
		HKWHubUUID = "XXX-XXX-XXX-XXX",
		SessionToken = "PubNub-1000",
		ResponseOf = "play_hub_media_selected_speakers",
		Result = true
		}
		
----

Play a Song from Web Server
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Plays a song from Web (http:) or rstp (rstp:) or mms (mms:) server. The URL of the song to play is specified by ``MediaUrl`` parameter.

.. note::

	``play_web_media`` does not specify speakers to play. It just uses the current session setting. If there is no speaker in the current session, then the play fails.
	
.. note::

	``play_web_media`` cannot be resumed. If it is paused by calling ``pause``, then it just stops playing music, and cannot resume.
	
	
- API: GET /api/v1/play_web_media?SessionToken=<session token>&MediaUrl=<URL of the song>
- Response
	- Play a song from HTTP server, and then return true or false.
- Example:
	- Request:
	
	.. code-block:: json
	
		http://<server_host_name>/api/v1/play_web_media?SessionToken=r:abciKaTbUgdpQFuvYtgMm0F&MediaUrl=http://seonman.github.io/music/hyolyn.mp3
			
	- Response: 

	.. code-block:: json

		{"Result":"true"}

.. Note::
	This API call takes several hundreds millisecond to return the response.

- PubNub
	- Publish Message

	.. code-block:: json

		{
		Command = "play_web_media",
		HKWHubUUID = "XXX-XXX-XXX-XXX",
		MediaUrl = "http://seonman.github.io/music/hyolyn.mp3"
		SesssionToken = "PubNub-1000"
		}

	- Message from HKWHub (via Subscribe)

	.. code-block:: json

		{
		HKWHubUUID = "XXX-XXX-XXX-XXX",
		SessionToken = "PubNub-1000",
		ResponseOf = "play_web_media",
		Result = true
		}
		
----

Play a Song from Web Server as party mode
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Plays a song from Web server with all speakers. The URL of the song to play is specified by ``MediaUrl`` parameter.

.. note::

	``play_web_media`` cannot be resumed. If it is paused by calling ``pause``, then it just stops playing music, and cannot resume.
	

- API: GET /api/v1/play_web_media_party_mode?SessionToken=<session token>&MediaUrl=<URL of the song>
- Response
	- Play a song from HTTP server to all speakers, and then return true or false.
- Example:
	- Request:
	
	.. code-block:: json
	
		http://<server_host>/api/v1/play_web_media_party_mode?SessionToken=r:abciKaTbUgdpQFuvYtgMm0F&MediaUrl=http://seonman.github.io/music/hyolyn.mp3
			
	- Response: 

	.. code-block:: json

		{"Result":"true"}

.. Note::
	This API call takes several hundreds millisecond to return the response.
	
- PubNub
	- Publish Message

	.. code-block:: json

		{
		Command = "play_web_media_party_mode",
		HKWHubUUID = "XXX-XXX-XXX-XXX",
		MediaUrl = "http://seonman.github.io/music/hyolyn.mp3"
		SesssionToken = "PubNub-1000"
		}

	- Message from HKWHub (via Subscribe)

	.. code-block:: json

		{
		HKWHubUUID = "XXX-XXX-XXX-XXX",
		SessionToken = "PubNub-1000",
		ResponseOf = "play_web_media_party_mode",
		Result = true
		}
	
----

Play a Song from Web Server with selected speakers
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Plays a song from Web server with selected speakers. The URL of the song to play is specified by ``MediaUrl`` parameter. The selected speakers are represented in ``DeviceIDList`` parameter as a list of ``DeviceID`` separated by ",".

.. note::

	``play_web_media`` cannot be resumed. If it is paused by calling ``pause``, then it just stops playing music, and cannot resume.

- API: GET /api/v1/play_web_media_selected_speakers?SessionToken=<session Token>&MediaUrl=<URL of the song>&DeviceIDList=<xxx,xxx,...>
- Response
	- Play a song from HTTP server to selected speakers, and then return true or false.
- Example:
	- Request:
	
	.. code-block:: json
	
		http://<server_host>/api/v1/play_web_media_selected_speakers?SessionToken=r:abciKaTbUgdpQFuvYtgMm0F&MediaUrl=http://seonman.github.io/music/hyolyn.mp3&DeviceIDList=34317244381360,129321920968880

	- Response: 

	.. code-block:: json

		{"Result":"true"}

.. Note::
	This API call takes several hundreds millisecond to return the response.
	
- PubNub
	- Publish Message

	.. code-block:: json

		{
		Command = "play_web_media_selected_speakers",
		HKWHubUUID = "XXX-XXX-XXX-XXX",
		MediaUrl = "http://seonman.github.io/music/hyolyn.mp3"
		SesssionToken = "PubNub-1000",
		DeviceIDList = "34317244381360,129321920968880"
		}

	- Message from HKWHub (via Subscribe)

	.. code-block:: json

		{
		HKWHubUUID = "XXX-XXX-XXX-XXX",
		SessionToken = "PubNub-1000",
		ResponseOf = "play_web_media_selected_speakers",
		Result = true
		}
	
----


Play TTS (Text-to-Speech)
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Plays a Text-to-Speech audio from VoiceRRS server. The Text to play is specified by ``Text`` parameter.

.. note::

	In order to use APIs for playing TTS (Text-To-Speech), you need to set VoiceRRS Application key on the setting menu of HKWHub App. You can go to the `VoiceRRS`_ web site to get your application key.

.. _`VoiceRRS`: http://www.voicerss.org/

.. note::

	``play_tts`` does not specify speakers to play. It just uses the current session setting. If there is no speaker in the current session, then the play fails.
	
.. note::

	``play_tts`` cannot be resumed. If it is paused by calling ``pause``, then it just stops playing music, and cannot resume.
	
	
- API: GET /api/v1/play_tts?SessionToken=<session token>&Text=<Text>
- Response
	- Play TTS audio, and then return true or false.
- Example:
	- Request:
	
	.. code-block:: json
	
		http://<server_host_name>/api/v1/play_tts?SessionToken=r:abciKaTbUgdpQFuvYtgMm0F&Text="Hello World. How are you today?"
			
	- Response: 

	.. code-block:: json

		{"Result":"true"}

.. Note::
	This API call takes more than several hundreds millisecond to return the response, depending on the network condition.

- PubNub
	- Publish Message

	.. code-block:: json

		{
		Command = "play_tts",
		HKWHubUUID = "XXX-XXX-XXX-XXX",
		Text = "Hello World. How are you today?"
		SesssionToken = "PubNub-1000"
		}

	- Message from HKWHub (via Subscribe)

	.. code-block:: json

		{
		HKWHubUUID = "XXX-XXX-XXX-XXX",
		SessionToken = "PubNub-1000",
		ResponseOf = "play_tts",
		Result = true
		}
		
----

Play TTS (Text-to-Speech) as party mode
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Plays a Text-to-Speech audio from VoiceRRS server with all speakers. The Text to play is specified by ``Text`` parameter.	

- API: GET /api/v1/play_tts_party_mode?SessionToken=<session token>&Text=<Text>
- Response
	- Play TTS audio to all speakers, and then return true or false.
- Example:
	- Request:
	
	.. code-block:: json
	
		http://<server_host>/api/v1/play_tts_party_mode?SessionToken=r:abciKaTbUgdpQFuvYtgMm0F&Text="Hello World. How are you today?"
			
	- Response: 

	.. code-block:: json

		{"Result":"true"}

.. Note::
	This API call takes several hundreds millisecond to return the response.
	
- PubNub
	- Publish Message

	.. code-block:: json

		{
		Command = "play_tts_party_mode",
		HKWHubUUID = "XXX-XXX-XXX-XXX",
		Text = "Hello World. How are you today?"
		SesssionToken = "PubNub-1000"
		}

	- Message from HKWHub (via Subscribe)

	.. code-block:: json

		{
		HKWHubUUID = "XXX-XXX-XXX-XXX",
		SessionToken = "PubNub-1000",
		ResponseOf = "play_tts_party_mode",
		Result = true
		}
	
----

Play a Song from Web Server with selected speakers
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Plays a Text-to-Speech audio from VoiceRRS server with selected speakers. The Text to play is specified by ``Text`` parameter. The selected speakers are represented in ``DeviceIDList`` parameter as a list of ``DeviceID`` separated by ",".

- API: GET /api/v1/play_tts_selected_speakers?SessionToken=<Session Token>&Text=<Text>&DeviceIDList=<xxx,xxx,...>
- Response
	- Play TTS from VoiceRSS server to selected speakers, and then return true or false.
- Example:
	- Request:
	
	.. code-block:: json
	
		http://<server_host>/api/v1/play_tts_selected_speakers?SessionToken=r:abciKaTbUgdpQFuvYtgMm0F&Text="Hello World. How are you today?"&DeviceIDList=34317244381360,129321920968880

	- Response: 

	.. code-block:: json

		{"Result":"true"}

.. Note::
	This API call takes several hundreds millisecond to return the response.
	
- PubNub
	- Publish Message

	.. code-block:: json

		{
		Command = "play_tts_selected_speakers",
		HKWHubUUID = "XXX-XXX-XXX-XXX",
		Text = "Hello World. How are you today?"
		SesssionToken = "PubNub-1000",
		DeviceIDList = "34317244381360,129321920968880"
		}

	- Message from HKWHub (via Subscribe)

	.. code-block:: json

		{
		HKWHubUUID = "XXX-XXX-XXX-XXX",
		SessionToken = "PubNub-1000",
		ResponseOf = "play_tts_selected_speakers",
		Result = true
		}
	
----


Pause the Current Playback
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Pauses the current playback. The client can resue the playback by ``resume_hub_media``.

- API: GET /api/v1/pause_play?SessionToken=<session token>
- Response
	- Pause the current playback, and then return true or false.
	- It can resume the current playback by calling ``resume_hub_media`` if and only if the playback is playing hub media. ``play_web_media`` cannot be resumed once it is paused or stopped.
- Example:
	- Request: 
	
	.. code-block:: json
	
		http://<server_host>/api/v1/pause_play?SessionToken=r:abciKaTbUgdpQFuvYtgMm0F

	- Response: 

	.. code-block:: json

		{"Result":"true"}

- PubNub
	- Publish Message

	.. code-block:: json

		{
		Command = "pause_play",
		HKWHubUUID = "XXX-XXX-XXX-XXX",
		SesssionToken = "PubNub-1000"
		}

	- Message from HKWHub (via Subscribe)

	.. code-block:: json

		{
		HKWHubUUID = "XXX-XXX-XXX-XXX",
		SessionToken = "PubNub-1000",
		ResponseOf = "pause_play",
		Result = true
		}
	
----

Resume the Current Playback with Hub Media
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

- API: GET /api/v1/resume_hub_media?SessionToken=<session token>&PersistentID=<persistent id>
- Response
	- Resume the current playback with Hub Media, and then return true or false.
- Example:
	- Request: 
	
	.. code-block:: json
	
		http://<server_host>/api/v1/resume_hub_media?SessionToken=r:abciKaTbUgdpQFuvYtgMm0F&PersistentID=7387446959931482519
		
	- Response: 

	.. code-block:: json

		{"Result":"true"}

- PubNub
	- Publish Message

	.. code-block:: json

		{
		HKWHubUUID = "XXX-XXX-XXX-XXX",
		Command = "resume_hub_media",
		PersistentID = 7387446959931482519,
		SesssionToken = "PubNub-1000"
		}

	- Message from HKWHub (via Subscribe)

	.. code-block:: json

		{
		HKWHubUUID = "XXX-XXX-XXX-XXX",
		SessionToken = "PubNub-1000",
		ResponseOf = "resume_hub_media",
		Result = true
		}
		
----

Resume the Current Playback with Hub Media as Party Mode
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

- API: GET /api/v1/resume_hub_media_party_mode?SessionToken=<session token>&PersistentID=<persistent id>
- Response
	- Resume the current playback with Hub Media with all speakers, and then return true or false.
- Example:
	- Request:
	
	.. code-block:: json
	
		http://<server_host>/api/v1/resume_hub_media_party_mode?SessionToken=r:abciKaTbUgdpQFuvYtgMm0F&PersistentID=7387446959931482519

	- Response: 

	.. code-block:: json

		{"Result":"true"}

- PubNub
	- Publish Message

	.. code-block:: json

		{
		HKWHubUUID = "XXX-XXX-XXX-XXX",
		Command = "resume_hub_media_party_mode",
		PersistentID = 7387446959931482519,
		SesssionToken = "PubNub-1000"
		}

	- Message from HKWHub (via Subscribe)

	.. code-block:: json

		{
		HKWHubUUID = "XXX-XXX-XXX-XXX",
		SessionToken = "PubNub-1000",
		ResponseOf = "resume_hub_media_party_mode",
		Result = true
		}
		
----

Resume the Current Playback with Hub Media with selected speakers
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

- API: GET /api/v1/resume_hub_media_selected_speakers?SessionToken=<session token>&PersistentID=<persistent id>&DeviceIDList=<xxx,xxx,...>
- Response
	- Resume the current playback with Hub Media with selected speakers, and then return true or false.
- Example:
	- Request:
	
	.. code-block:: json

		http://<server_host>/api/v1/resume_hub_media_selected_speakers?SessionToken=r:abciKaTbUgdpQFuvYtgMm0F&PersistentID=7387446959931482519&DeviceIDList=34317244381360,129321920968880

	- Response: 

	.. code-block:: json

		{"Result":"true"}

- PubNub
	- Publish Message

	.. code-block:: json

		{
		HKWHubUUID = "XXX-XXX-XXX-XXX",
		Command = "resume_hub_media_selected_speakers",
		PersistentID = 7387446959931482519,
		SesssionToken = "PubNub-1000",
		DeviceIDList = "34317244381360,129321920968880"
		}

	- Message from HKWHub (via Subscribe)

	.. code-block:: json

		{
		HKWHubUUID = "XXX-XXX-XXX-XXX",
		SessionToken = "PubNub-1000",
		ResponseOf = "resume_hub_media_selected_speakers",
		Result = true
		}
----

Stop the Current Playback
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

- API: GET /api/v1/stop_play?SessionToken=<session token>
- Response
	- Stop the current playback with Hub Media, and then return true or false.
	- If the playback has stopped, then it cannot resume.
- Example:

	- Request: 
	
	.. code-block:: json
	
		http://<server_host>/api/v1/stop_play?SessionToken=r:abciKaTbUgdpQFuvYtgMm0F
		
	- Response: 

	.. code-block:: json

		{"Result":"true"}

- PubNub
	- Publish Message

	.. code-block:: json

		{
		HKWHubUUID = "XXX-XXX-XXX-XXX",
		Command = "stop_play",
		SesssionToken = "PubNub-1000",
		}

	- Message from HKWHub (via Subscribe)

	.. code-block:: json

		{
		HKWHubUUID = "XXX-XXX-XXX-XXX",
		SessionToken = "PubNub-1000",
		ResponseOf = "stop_play",
		Result = true
		}
----

Get the Playback Status (Current Playback State and Elapsed Time)
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

- API: GET /api/v1/playback_status?SessionToken=<session token>
- Response
	- It returns the current state of the playback and also return the elapsed time (in second) of the playback.
	- If it is not playing, then the elapsed time is (-1)
	- The following is the value of each playback state:
		- PlayerStatePlaying : Now playing audio
		- PlayerStatePaused : Playing is paused. It can resume.
		- PlayerStateStopped : Playing is stopped. It cannot resume.

	- Note that if the playback has stopped, then it cannot resume.
	- Developers need to check the playback status during the playback to handle any possible exceptional cases like interruption or errors. We recommedn to call this API every second.
- Example:
	- Request: 
	
	.. code-block:: json
	
		http://<server_host>/api/v1/playback_status?SessionToken=r:abciKaTbUgdpQFuvYtgMm0F
		
	- Response: 

	.. code-block:: json

		{"PlaybackState":"PlayerStatePlaying",
		 "TimeElapsed":"15"}
		 
- PubNub
	- PubNub mode does not support playback_status command, because clients subscribing the channel will automatically receive the playback_status event from the HKWHub app when an event is available.
	- Event from HKWHub app

	.. code-block:: json

		{
		HKWHubUUID = "XXX-XXX-XXX-XXX",
		SessionToken = "PubNub-1000",
		Envet = PlaybackTimeChanged,
		PlaybackTime = 15
		}
	

----

Check if the Hub is playing audio
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

- API: GET /api/v1/is_playing?SessionToken=<session token>
- Response
	- Returns true (playing) or false (not playing)
- Example:
	- Request: 
	
	.. code-block:: json
	
		http://<server_host>/api/v1/is_playing?SessionToken=r:abciKaTbUgdpQFuvYtgMm0F
		
	- Response: 

	.. code-block:: json

		{"IsPlaying":"true"}

- PubNub
	- Publish Message

	.. code-block:: json

		{
		HKWHubUUID = "XXX-XXX-XXX-XXX",
		Command = "is_playing",
		SesssionToken = "PubNub-1000",
		}

	- Message from HKWHub (via Subscribe)

	.. code-block:: json

		{
		HKWHubUUID = "XXX-XXX-XXX-XXX",
		SessionToken = "PubNub-1000",
		ResponseOf = "is_playing",
		Result = true
		}
		
		
Volume Control
~~~~~~~~~~~~~~~~~

Get Volume for all Devices
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

- API: GET /api/v1/get_volume?SessionToken=<session token>
- Response
	- Returns the average volume of all devices.
	- The range of volume is 0 (muted) to 50 (max)
- Example:
	- Request: 
	
	.. code-block:: json
	
		http://<server_host>/api/v1/get_volume?SessionToken=r:abciKaTbUgdpQFuvYtgMm0F
		
	- Response: 

	.. code-block:: json

		{"Volume":"10"}

- PubNub
	- Publish Message

	.. code-block:: json

		{
		Command = "get_volume",
		HKWHubUUID = "XXX-XXX-XXX-XXX",
		SesssionToken = "PubNub-1000"
		}

	- Message from HKWHub (via Subscribe)

	.. code-block:: json

		{
		HKWHubUUID = "XXX-XXX-XXX-XXX",
		ResponseOf = "get_volume",
		SesssionToken = "PubNub-1000",
		Volume = 10
		}
		
----

Get Volume for a particular device
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

- API: GET /api/v1/get_volume_device?SessionToken=<session token>&DeviceID=<device id>
- Response
	- Returns the  volume of a particular device
	- The range of volume is 0 (muted) to 50 (max)
- Example:
	- Request: 
	
	.. code-block:: json

		http://<server_host>/api/v1/get_volume_device?SessionToken=r:abciKaTbUgdpQFuvYtgMm0F&DeviceID=1234567
		
	- Response: 

	.. code-block:: json

		{"Volume":"10"}

- PubNub
	- Publish Message

	.. code-block:: json

		{
		Command = "get_volume_device",
		HKWHubUUID = "XXX-XXX-XXX-XXX",
		SesssionToken = "PubNub-1000",
		DeviceID=1234567
		}

	- Message from HKWHub (via Subscribe)

	.. code-block:: json

		{
		HKWHubUUID = "XXX-XXX-XXX-XXX",
		ResponseOf = "get_volume_device",
		SesssionToken = "PubNub-1000",
		Volume = 10
		}

----

Set Volume for all devices
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

- API: GET /api/v1/set_volume?SessionToken=<session token>&Volume=<volume>
- Response
	- Returns true or false
- Example:
	- Request: 
	
	.. code-block:: json
	
		http://<server_host>/api/v1/set_volume?SessionToken=r:abciKaTbUgdpQFuvYtgMm0F&Volume=10
		
	- Response: 

	.. code-block:: json

		{"Result":"true"}

- PubNub
	- Publish Message

	.. code-block:: json

		{
		Command = "set_volume",
		HKWHubUUID = "XXX-XXX-XXX-XXX",
		SesssionToken = "PubNub-1000",
		Volume = 10
		}

	- Message from HKWHub (via Subscribe)

	.. code-block:: json

		{
		HKWHubUUID = "XXX-XXX-XXX-XXX",
		ResponseOf = "set_volume",
		SesssionToken = "PubNub-1000",
		Result = true
		}
		
----

Set Volume for a particular device
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

- API: GET /api/v1/set_volume_device?SessionToken=<session token>&DeviceID=<device id>&Volume=<volume>
- Response
	- Returns true or false
- Example:
	- Request: 
	
	.. code-block:: json
	
		http://<server_host>/api/v1/set_volume_device?SessionToken=r:abciKaTbUgdpQFuvYtgMm0F&DeviceID=1234567&Volume=10
		
	- Response: 

	.. code-block:: json

		{"Result":"true"}

- PubNub
	- Publish Message

	.. code-block:: json

		{
		Command = "set_volume_device",
		HKWHubUUID = "XXX-XXX-XXX-XXX",
		SesssionToken = "PubNub-1000",
		DeviceID = 1234567,
		Volume = 10
		}

	- Message from HKWHub (via Subscribe)

	.. code-block:: json

		{
		HKWHubUUID = "XXX-XXX-XXX-XXX",
		ResponseOf = "set_volume_device",
		SesssionToken = "PubNub-1000",
		Result = true
		}

----
		
Device Status Change Event (only available for PubNub mode)
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
- PubNub
	- Whenever a speaker status change occurs, the HKWHub sends DeviceStateUpdated event to subscribers.
	- Type of Reason
		- NetworkUnavailable : the network became unavailable
		- DeviceAvailable : a device became available
		- DeviceUnavailable : a device becaome unavailable
		- DeviceError : some error occured to a particular speaker
		- SpeakerInfoUpdated: some of speaker information has been updated
		- WiFiSignalChanged: wifi signal has changed
		
	- Example:
	
	.. code-block:: json

		{
		Event = DeviceStateUpdated,
		HKWHubUUID = "XXX-XXX-XXX-XXX",
		Reason = SpeakerInfoUpdated,
		SessionToken = "PubNub-1000"
		}

----


OAuth2 Authorization API Specification
-------------------------------------------------------

Introduction
~~~~~~~~~~~~~~~~

In order to access the HKIoTCloud REST APIs to control Omni speakers, your HKIoTCloud-enabled product needs to obtain a HKIoTCloud access token that grans access to the APIs on behalf of the product's user.

.. NOTE::

	Please refer `OAuth 2.0 Getting Started in Web-API Security by Matthias Biehl`_ for your more understanding on OAuth2.
	
.. _OAuth 2.0 Getting Started in Web-API Security by Matthias Biehl: http://www.amazon.com/OAuth-2-0-Getting-Security-University/dp/1507800916/ref=tmm_pap_swatch_0?_encoding=UTF8&qid=1454629444&sr=8-1
	
The workflow for obtaining and using an access token is as follows:

1. The user visits your product registration website and enters information about their specific instance of your product.
2. Your website creates a HKIoTCloud consent request using the user-supplied registration information and forwards the user to the HKIoTCloud website.
3. The user logs in to HKIoTCloud.
4. The user authorizes their instance of your product to be used with HKIoTCloud on their behalf.
5. HKIoTCloud returns an access token to your product registration website.
6. Your product registration website securely transfers the access token to the user's specifi instance of your product.
7. The user's speific instance of your product uses the access token to make HKIoTCloud API calls.

Types of Authorization
~~~~~~~~~~~~~~~~~~~~~~~~~

HKIoTCloud supports two types of authorization:


- Authorization Code Grant - Send a client ID and a client secret to get an access token and a refresh token.

- Password Grant - Send username and password along with client ID and client secret to get an access token and refresh token

.. note::

	If you are able to implement server-side scripting, then using authorization code grant is recommended. If you are not able to implement server-side scripting, then using password grant is your choice.
	
.. Note::

	You must generate a new access token every hour, that is, expiration is set to 3,600 seconds. You can use refresh token in conjunction with your client ID and client secret to obtain a new access token without your user having to re-authenticate.
	

Using the Password Grant Type
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

To obtain an access token (and a refresh token) with password grant, you should **POST** to ``/oauth/token``. You should include your client ID and client secret in the ``Authorization`` header by combining them with a colon ":" and then encoding in Base64. That is, ``Base64(client_id:client_secret)``. And also, you should include ``grant_type: password``, username and passworkd in the request body.

**Sample Request:**

.. code::

	POST /oauth/token HTTP/1.1
	Host: hkiotcloud.herokuapp.com
	Authorization: Basic RkZjUE9iS2h4OThvNXhtMzpjcENZQ1BrUjA4NXFSR3hFempDMUlGeEoxQWRhZFQ=
	Content-Type: application/x-www-form-urlencoded  

	grant_type=password&username=johndoe&password=A3ddj3w 

Here, ``RkZjUE9iS2h4OThvNXhtMzpjcENZQ1BrUjA4NXFSR3hFempDMUlGeEoxQWRhZFQ=`` is the result of Base64 encoding of clientId:clientSecret.

**Sample Response:**

.. code::

	HTTP/1.1 200 OK
	Content-Type: application/json;charset=UTF-8
	Cache-Control: no-store
	Pragma: no-cache
 
	{
	   "access_token":"62b8c11cfa0840b506230cb8af747230052775e1",
	   "token_type":"bearer",
	   "expires_in":3600,
	   "refresh_token":"7a7687b6b32247573b366d5bf2eeb707ba0a1b4d"
	 }



Creating a Consent Request
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

By creating a consent request, your user will be redirected to the HKIoTCloud website where they can enter their HKIoTCloud credentials in order to authorize their devices of your product to be used with the HKIoTCloud service.

The consent request is constructed as follows:

- Redirect the user to HKIoTCloud at https://hkiotcloud.herokuapp.com/oauth/token with the following URL-encoded query parameters:
	- ``client_id`` : The client ID of your application. This information can be found on the HKIoTCloud website.
	- ``response_type``: ``code`` for authorization code grant.
	- ``redirect_uri`` : Specifies the return URI that you added to your app's  profile when signing up.

**Sample Request:**

Send as GET request.

.. code-block:: json

	https://hkiotcloud.herokuapp.com/oauth/authorize?response_type=code&client_id=n7HhiTnKYjJd4zmM&redirect_uri=https://your.app.com/oauthCallbackHKIoTCloud


HKIoTCloud Returns a Response to Your Registration Website
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

After the user is authenticated, the user is redirected to the URI that you provided in the ``redirect_uri`` parameter of the request.

The response includes an authorization code.

**Sample Authorizatino Code Grant Response:**

.. code-block:: json

	https://your.app.com/oauthCallbackHKIoTCloud?code=0b368d49809048dd7424d6f7fd869a98f2372859


Next, your service leverages the returned authorization code to ask for an access token:

- Send a **POST** request to https://hkiotcloud.herokuapp.com/oauth/token with the following parameters:

**HTTP Header Parameters:**

- ``Content-Type: application/x-www-form-urlencoded``

**HTTP Body Parameters:**

- ``grant_type: authorization_code``
- ``code`` : The authorization code that was returned in the response.
- ``client_id`` : Your application's client ID. This information can be found on the HKIoTCloud website.
- ``client_secret`` : The application's client secret. This information can be found on the HKIoTCloud website.
- ``redirect_uri`` : The return URI that you added to your app's profile when signing up.

**Sample Request:**

.. code-block:: json

	POST /oauth/token HTTP/1.1
	Host: hkiotcloud.herokuapp.com
	Content-Type: application/x-www-form-urlencoded
	Cache-Control: no-cache
 
	grant_type=authorization_code&code=2b3711911f4f2263e785eeda386046ccc8da6aee&client_id=n7HhiTnKYjJd4zmM&client_secret=ANRfB9z94xtcxFGXrd5XHXEiKg43UY&redirect_uri=https://hkvoicecloud.herokuapp.com/oauthCallbackHKIoTCloud


**Sample Response:**

.. code-block:: json

	{
		"access_token": "902da699ed1d5d511bd750366889f3260c2015b4",
		"expires_in": 3600,
		"refresh_token": "5defcb0a9a49ac9b2403b8c78600638238d81011",
		"token_type": "bearer"
	}	


Transfer the access and refresh tokens to the user's product.

.. NOTE::
	
	Currently, a refresh token is valid for one year, while an access token is valid only an hour and an authorization code is valid only a minute.



Using the Access Token to Make HKIoTCloud API Calls
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

When you call the HKIoTCloud API calls, pass the value of the access token into the request header. Specifically, create an ``Authorization`` header and give it the value ``Bearer <access token>``.

**Sample Request using curl:**

- curl -X GET -H "Authorization: Bearer 15c0507f3a550d7a31f7af5dc45e4dd9fd9f4bc8" http://hkiotcloud.herokuapp.com/api/v1/init_session


Getting a New Access Token with Refresh Token
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

The access token is valid for one hour. When the access token expires or is about to expire, you can exchange the refresh token for new access and refresh tokens.

- Send a ``POST`` request to ``https://hkiotcloud.herokuapp.com/oauth/token`` with the following parameters:

**HTTP Header Parameters:**

- ``Content-Type: application/x-www-form-urlencoded``

**HTTP Body Parameters:**

- ``grant_type: refresh_token``
- ``refresh_token`` : The refresh token returned with the last request for a new access token.
- ``client_id`` : Your application's client ID. This information can be found on the HKIoTCloud website.
- ``client_secret`` : The application's client secret. This information can be found on the HKIoTCloud website.

**Sample Request:**

.. code-block:: json

	POST /oauth/token HTTP/1.1
	Host: hkiotcloud.herukuapp.com
	Content-Type: application/x-www-form-urlencoded
	Cache-Control: no-cache
 
	grant_type=refresh_token&refresh_token=5defcb0a9a49ac9b2403b8c78600638238d81011&client_id=n7HhiTnKYjJd4zmM&client_secret=ANRfB9z94xtcxFGXrd5XHXEiKg43UY


**Sample Response:**

.. code-block:: json

	HTTP/1.1 200 OK
	 
	{
		"access_token": "90da03bdceb15cf75d99ff99715ce87b29602651",
		"expires_in": 3600,
		"refresh_token": "6a762dfce9146dbf149f881c5aa15fc6cfdf1fd0",
		"token_type": "bearer"
	}

