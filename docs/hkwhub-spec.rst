HKWHub Specification
==================================

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

- API: GET /v1/close_session?SessionID=1000
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

- API: GET /v1/device_count?SessionID=1000
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
		[{"GroupName":"Bathroom", "Role":21, "MacAddress":"b0:38:29:1b:36:1f", "WifiSignalStrength":-47, "Port":44055, "Active":true, "DeviceName":"Adapt1", "Version":"0.1.6.2", "ModelName":"Omni Adapt", "IPAddress":"192.168.1.40", "GroupID":"3431724438", "Volume":47, "IsPlaying":false, "DeviceID":"34317244381360"},
		{"GroupName":"Temp", "Role":21, "MacAddress":"b0:38:29:1b:9e:75", "WifiSignalStrength":-53, "Port":44055, "Active":true, "DeviceName":"Adapt", "Version":"0.1.6.2", "ModelName":"Omni Adapt", "IPAddress":"192.168.1.39", "GroupID":"1293219209", "Volume":47, "IsPlaying":false, "DeviceID":"129321920968880"}]
	}
	
Get the Device Information
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

- API: GET /v1/device_info?SessionID=<session id>&DeviceID=<device id>
- Response
	- Returns the information of the device
- Example:
	- Request: ``http://192.168.1.10/v1/device_info?SessionID=1000&DeviceID="129321920968880"``
	- Response: 

.. code-block:: json

	{"GroupName":"Temp", "Role":21, "MacAddress":"b0:38:29:1b:9e:75", "WifiSignalStrength":-52, "Port":44055, "Active":true, "DeviceName":"Adapt", "Version":"0.1.6.2", "ModelName":"Omni Adapt", "IPAddress":"192.168.1.39", "GroupID":"1293219209", "Volume":47, "IsPlaying":true, "DeviceID":"129321920968880"}