Version History (iOS)
=======================

V1.2 - Jul. 8 , 2015
----------------------

- HKWirelessHDSDK supports two versions of SDK: normal version and lightweight version
	- HKWirelessHDSDK (normal version) - support for playback of web streaming audio
	- HKWirelessHDSDKlw (lightweight version) : lightweight version : no support for web streaming audio
- Added mute(), unmute(), isMuted()
- New sample apps
	- HKWPlayer app: a sample player app with full usage of HKWirelessHD APIs. Apple Watch support.
	- HKWHub app : Web hub app for handling REST API request
	- HKWSimple app: a very simple player app

V1.1 - Apr. 21, 2015
----------------------

- Use delegate patterns for receiving events from HKWControlHandler.
	- Added HKWDeviceEventHandlerDelegate.h and HKWPlayerEventHandlerDelegate.h

V1.0 - Jan, 2015
-------------------

- Initial version