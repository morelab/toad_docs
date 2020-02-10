==============
Extensibility
==============

Add devices
------------
Adding a device, means to integrate the device into the current
system. As the system is composed of modular components that are
connected by an MQTT broker, you only need to create an agent that
communicates with the device and it publishes to a specific MQTT
topic where the data is stored into InfluxDB.

For fully integrating the devices with InfluxDB and its REST API,
you must publish your devices data to the following MQTT topics:

 #TODO

Apart from using specific topics, the published data must obey a
specific format:

 #TODO

Add API calls
--------------
We have only implemented an API for controlling the SmartPlugs
and a second API for querying data from InfluxDB.

You must realize that the REST API component does not implement
any logic for executing those requests, it is a dumb component
that takes the API call and it transforms it into a MQTT topic
and payload. So, for adding new API calls you must know how are
the API calls transformed into MQTT messages, and you have to
implement a component that listens to them.

Now, we explain how the REST API calls are transformed into MQTT messages:

 #TODO


Add others: frameworks, databases, etc.
----------------------------------------
For adding new frameworks such as Linksmart_, Fiware_ or any
other, the best approach is to implement a component that
listens to the topics where data is published, so that intercepts
the data and republishes to the required framework. Also, you
can listen to the topics where control commands or data queries
are published.

By default, the data is solely published to Influx, so these are
the topics, where your framework middleware should listen:

 #TODO

.. _Fiware: https://www.fiware.org/
.. _Linksmart: https://linksmart.eu/
