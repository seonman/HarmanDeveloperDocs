Getting Started Guide (iOS)
===========================

HKWirelessHD SDK supports both Objective-C and Swift. This document assumes that developer creates his/her app using the Swift language.

In the section, we will use the HKPage app as an example.


Project Setup with HKWireless (normal version)
----------------------------------------------

Include HKWirelessHDSDK into your project
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

- Add HKWirelessHDSDK to your project by dragging and dropping the HKWirelessHDSDK folder into the project navigator. Select “Create groups”, and click finish. By doing this, the include header path for HKWirelessHD SDK is added to your project. We need only the “include” folder, so you may remove the references to the other items in the folder. (Right click on any items you want to remove, select “Delete”, and then select “Remove References”.)

Add libHKWirelessHD.a as link binary
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

- Project Setting > Your Targets > Build Phases > Link Binary With Libraries 
	- Click '+', and then click "Add Other..."
	- Find 'libHKWirelessHD.a' in HKPage/HKWirelessHDSDK/lib, and add it.

1.	Go to Project Setting > Build Setting > Swift Compiler - Code Generation > Objective-C Bridging Header

After adding the HKWirelessHDSDK folder into your project and adding libHKWirelessHD.a, the project navigator will look like as below:

.. figure:: img/getting-started-iOS/project-setting-2.png

Add Swift Bridging Header
~~~~~~~~~~~~~~~~~~~~~~~~~

HKWirelessHD SDK has been written in C++ and Objective-C. Therefore, when you use the SDK in Swift, you should include Swift Bridging Header in your project. To do this:

1. Go to Project Setting > Build Setting > Swift Compiler - Code Generation > Objective-C Bridging Header

.. figure:: img/getting-started-iOS/project-setting-3.png

2. Add [My-Project-Name]/[My-Project-Name]-Bridging-Header.h

	- In our example, it looks like : HKPage/HKPage-Bridging-Header.h

3. Add the following lines in the header file.

.. code-block:: swift

	#import "HKWControlHandler.h"
	#import "HKWPlayerEventHandlerSingleton.h"
	#import "HKWDeviceEventHandlerSingleton.h"

Add -lstdc++ as linker flag
~~~~~~~~~~~~~~~~~~~~~~~~~~~

- Project Setting > Your Targets > Build Settings > Linking > Other Linder Flags
	- Add “-lstdc++” in the text field

.. figure:: img/getting-started-iOS/project-setting-4.png


Using HKWirelessHD API
----------------------

All APIs can be accessed through a singleton object of HKWControlHandler. Only you have to do is obtain the singleton instance of HKWControlHandler and use it to invoke whatever API you want to call.

The code examples in this document are in Swift. and the function definition is in Objective-C. The APIs are compatible both in Swift and Objective-C.

Initialize HKWirelessHD Control Handler and start the Wireless Audio
~~~~~~~~~~~~~~~~~~~~

.. code-block:: swift

	// Obtain the HKWControlHandler singleton instance
	let g_HWControlHandler: HKWControlHandler = HKWControlHandler.sharedInstance()

	// Initialize the HKWControlHandler and start wireless audio
	g_HWControlHandler.initializeHKWirelessController (licenseKey)

``initializeHKWirelessController()`` takes a string of license key as parameter. Every developer who signs up for Harman developer community will receive a license key. If you have not received it yet, then just use the license key specified in the sample code for temporary use until your own license key is available.

.. note:: 
	``initializeHKWirelessController()`` is a blocking call. It waits until the call successfully initializes the wireless audio network. If the phone device does not belong to a Wi-Fi network or if there is other app already using HKWirelessHD and running on the same device, then it will wait until the other app releases the HKWControlHandler. It would be nice to present a dialog to user before calling ``initializeHKWirelessController()`` to notice that the app will wait until HKWirelessHD network is available. 


Discovery and refreshing of available speakers in the Wi-Fi network
~~~~~~~~~~~~~~~~~~~~~~

The status of speakers can be changed dynamically over time. And, whenever a speaker is turned off or on, the list of speakers available in the network should be refreshed. Especially, when you select speakers for playback, the speaker list and the status of each speaker should be updated with the latest information.

Basically, whenever there is any change on the speaker side, the newest information of the speaker is available from the device list maintained by HKWControlHandler. But, the update is initiated by speakers, and there is some latency until the latest information is reflected to the HKWControlHandler. So, if you need the latest information without latency, you would better refresh the speaker status regularly.

To force to update the status of speakers regularly, the SDK provides a pair of convenient APIs to refresh device status. One of the use cases of these functions are to present a screen of speaker list to user and show the current speaker information in real-time manner.

To start checking the status of devices regularly, use ``startRefreshDeviceInfo()``. To stop checking the status regularly, use ``stopRefreshDeviceInfo()``.

.. code-block:: swift

	// start to refresh devices ... 
	g_HKWControlHandler.startRefreshDeviceInfo()
	
	// stop to refresh devices
	g_HKWControlHandler.stopRefreshDeviceInfo()  

``startRefreshDeviceInfo()`` will refresh and update every 2 seconds the status of the devices in the current Wi-Fi network.

.. note:: 
	Even without calling ``startRefreshDeviceInfo()``, the speaker information will be updated whenever the information is updated on speaker side, but there is some latency until the newest information is reflected to HKWControlHandler.


Speakers and Groups
~~~~~~~~~~~~~~~~~~~

There are two ways to choose speakers to play on – one is to select a speaker from the global list of speakers maintained by the internal data structure, and the other is to select a speaker with a group (or room) index and the index of the speaker within the group. Note that in this document, the term group and room are used as the same meaning, that is, a set of speakers.

**Selecting a speaker individually**

***Select a speaker in the global list***

.. code-block:: swift

	// get the number of available speakers
	let deviceCount = g_HKWControlHandler.getDeviceCount()
	
	// get the info of the first devices in the list
	var index = 0
	let deviceInfo = g_HKWControlHandler.getDeviceInfoByIndex(index)

***Retrieve DeviceInfo with deviceId***

If you know the deviceId of a speaker, then you can retrieve the device information using ``findDeviceFromList()``.

.. code-block:: swift

	// get the number of available speakers
	var deviceId : ClongLong = …
	let deviceInfo = g_HKWControlHandler.findDeviceFromList(deviceId)

**Selecting a speaker from a group**

A **Group** is defined by the group information of each speaker. That is, if a speaker has a group where it belongs to, then the group has the speaker as a member. So, as an example, if speaker A and speaker B have the same group of Group C, then Group C will have speaker A and speaker B as members. If speaker A changes the group as ‘Group D’, then Group C will have only speaker B, and Group D will have speaker A as a member.

***Get the number of groups available in the network***

.. code-block:: swift

	// get the number of groups
	var groupCount = g_HWKControlHandler.getGroupCount()


Walkthrough
-----------

Step 1: Register a developer account
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

If you haven't already, `Register for a developer account <https://graph.api.smartthings.com/register/developer>`__

---- 

Step 2: Go the developer environment page
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Head over to the `developer environment page <https://graph.api.smartthings.com>`__. This is where you can manage your hubs, devices, view logging, and more. We're going to use the web-based IDE to create a SmartApp.

----

Step 3: Create your SmartApp
~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Click on the "My SmartApps" link:

.. image:: img/quick-start/dev_hub_smartapp_menu.png

This will take you to your SmartApps page, where you can view and manage your SmartApps. Press the "New SmartApp" button on the right of the page:

.. image:: img/quick-start/ide_new_smartapp_button.png

Give your app a name, author, and description. Set the category to "My Apps". Then click the "Create" button.

.. image:: img/quick-start/new-smart-app-form.png

This will take you to the IDE, where you will see some code has been filled in for you.

There are three core methods that must be defined for SmartApps:

- ``preferences`` is where we configure what information we need from the user to run this app. 
- ``installed`` is the method that is called when this app is installed. Typically this is where we subscribe to events from configured devices.
- ``updated`` is the method that is called when the preferences are updated. Typically just unsubscribes and re-subscribes to events, since the preferences have changed.

Our example is going to be pretty simple - we will create an app that triggers a light to come on when a door opens.

At a high level, our app will need to:

#. Gather the devices (door and light) to use for this app
#. Monitor the door device - if it is opened, turn the light on. If it is closed, turn it off.
        
----

Step 4: Fill in the preferences block
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

The first thing we need to do is gather the sensors and switches we want this SmartApp to work with. We do this through the ``preferences`` definition.

In the IDE, replace the generated preferences block with the following:

.. code-block:: groovy

    preferences {
        // What door should this app be configured for?
        section ("When the door opens/closes...") {
            input "contact1", "capability.contactSensor", 
                  title: "Where?"
        }
        // What light should this app be configured for?
        section ("Turn on/off a light...") {
            input "switch1", "capability.switch"
        }
    }


Click the "Save" button above the editor.

.. note::

    When interacting with devices, SmartApps should use capabilities to ensure maximum flexibility (that's the "capability.contactSensor" above). The available capabilities can be found on the :ref:`capabilities_taxonomy` page.

    More information about preferences can be found in the `Preferences and Settings section <smartapp-developers-guide/preferences-and-settings.html>`__ of the `SmartApp Developer's Guide <smartapp-developers-guide/index.html>`__. 

----

Step 5: Subscribe to events
~~~~~~~~~~~~~~~~~~~~~~~~~~~

In the IDE, note that there is an empty ``initialize`` method defined for you. This method is called from both the ``installed`` and ``updated`` methods. 

This is where we will subscribe to the device(s) we want to monitor. In our case, we want to know if the door opens or closes.

Replace the ``initialize`` method with this:

.. code-block:: groovy

    def initialize() {
        subscribe(contact1, "contact", contactHandler)
    }

Note the arguments to the subscribe method. The first argument, "contact1", corresponds to the name in the preferences input for the contact sensor. This tells the SmartApp executor what input we are subscribing to. The second parameter, "contact", is what value of the sensor we want to listen for. In this case, we use "contact" to listen to all value changes (open or closed). The third parameter, "contactHandler", is the name of a method to call when the sensor has a state change. Let's define that next!

(don't forget to click the "Save" button!)

.. note::


    More information about events and subscriptions can be found in the `Events and Subscriptions section <smartapp-developers-guide/simple-event-handler-smartapps.html>`__ of the `SmartApp Developer's Guide <smartapp-developers-guide/index.html>`__. 

----

Step 6: Define the event handler
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Add the following code to the bottom of your SmartApp:

.. code-block:: groovy

    // event handlers are passed the event itself
    def contactHandler(evt) { 
        log.debug "$evt.value"
    
        // The contactSensor capability can be either "open" or "closed"
        // If it's "open", turn on the light! 
        // If it's "closed" turn the light off.
        if (evt.value == "open") {
            switch1.on();
        } else if (evt.value == "closed") {
            switch1.off();
        }
    }

Click the "Save" button, and let's try it out!

----

Step 7: Run it in the simulator
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

To the right of the editor in the IDE, you should see a "Location" field:

.. image:: img/quick-start/ide-set-location.png

Select the location of your hub (if you have only one hub, it will be selected by default), and click "Set Location". 

Now you can pick some devices if you have them, or create some virtual devices. 

.. image:: img/quick-start/ide-install-app.png

Once you've picked some devices, click "Install" to launch the simulator:

.. image:: img/quick-start/ide-simulator.png

Try changing the contact sensor from closed to open - you should see the switch in the simulator turn on. If you used a real switch, you should see the light actually turn on or off! 

Also note the log statements in the log console. Logging is extremely useful for debugging purposes.

----

Bonus Step: Publish your SmartApp (for you only)
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

We've run our app in the simulator, which is a great way to test as we develop. But we can also publish our app so we 
can use it from our smart phone, just like other SmartApps. Let's walk through those steps.

On top of the IDE, there's a "Publish" button right next to the Save button. Click it, and select "For me":

.. image:: img/quick-start/ide-publish-for-me.png

You should see a message indicating your app published successfully.

On your mobile phone, launch the SmartThings app, and go to the Dashboard. Towards the bottom, click the "+" icon:

.. image:: img/quick-start/mobile-install-my-app.png

In the SmartSetup screen, scroll all the way to the right to select "My Apps". You should see your app there - select it and you can install it just like any other SmartApp! (you'll need physical devices to successfully install this app)

.. image:: img/quick-start/mobile-myapps-install.png

Next Steps
----------

This tutorial has shown you how to set up a developer account, use the IDE to create a simple SmartApp, use the simulator to test your SmartApp, and publish your SmartApp to your mobile phone. 

In addition to using this documentation, the best way to learn is by looking at existing code and writing your own. In the IDE, there are several templates that you can review. These are great sources for learning SmartThings development! In fact, the SmartApp we built borrows heavily from (OK, it's a total clone) the "Let There Be Light" SmartApp. 










