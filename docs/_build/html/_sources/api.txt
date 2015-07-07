.. _smartapp_ref:

API Documentation
===================


Initialization
------------------

The following methods should be defined by all SmartApps. They are called by the SmartThings platform at various points in the SmartApp lifecycle.

- initializeHKWirelessController
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Initializes and starts HKWirelessHD controller. This API requires a license key as string. If the input license key fails in key validation, then the API returns -1 as result. If it is successful, return 0. <p>This is a blocking call, and it will not return until the caller successfully initializes HKWireless controller. If the phone is not connected to a Wi-Fi network, or any other app on the same phone is using the HKWireless controller, it waits until the app releases the controller.

If you needs non-blocking behavior, you should call this API asynchronously using other thread.

.. note::

    This method is expected to be defined by SmartApps.

Called when an instance of the app is installed. Typically subscribes to events from the configured devices and creates any scheduled jobs.

**Signature:**
    ``- (NSInteger) initializeHKWirelessController:(NSString*)licenseKey;``

**Returns:**
    NSInteger

**Example:**

.. code-block:: groovy

    def installed() {
        log.debug "installed with settings: $settings"

        // subscribe to events, create scheduled jobs.
    }

----
