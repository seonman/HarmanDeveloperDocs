.. _smartapp_ref:

SmartApp
========

A SmartApp is a Groovy-based program that allows developers to create automations for users to tap into the capabilities of their devices.

They are created through the "New SmartApp" action in the IDE. There is no "class" for a SmartApp per se, but there are various methods and properties available to SmartApps that are documented below.

When a SmartApp executes, it executes in the context of a certain installation instance. That is, a user installs a SmartApp on their mobile application, and configures it with devices or rules unique to them. A SmartApp is not continuously running; it is executed in response to various schedules or subscribed-to events.

----

The following methods should be defined by all SmartApps. They are called by the SmartThings platform at various points in the SmartApp lifecycle.

installed()
~~~~~~~~~~~

.. note::

    This method is expected to be defined by SmartApps.

Called when an instance of the app is installed. Typically subscribes to events from the configured devices and creates any scheduled jobs.

**Signature:**
    ``void installed()``

**Returns:**
    void

**Example:**

.. code-block:: groovy

    def installed() {
        log.debug "installed with settings: $settings"

        // subscribe to events, create scheduled jobs.
    }

----
