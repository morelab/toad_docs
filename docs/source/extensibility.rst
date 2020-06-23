==============
Extensibility
==============

Add SmartPlugs
---------------
In order to add a SmartPlug these are the steps to follow:

1. Install the SmartPlug.
2. Assign an IP to the SmartPlug.
3. In the Virtual Machine IoToad is deployed, go to :code:`~/toad_main/config/toad_sp_data/config.ini`
   and edit the :code:`IP_RANGE_START` < ip < :code:`IP_RANGE_STOP`, so that it includes the new SmartPlug IP.
4. Update ETCD database so that it includes MAC-->SmartPlugID mapping. In order to update it,
   the ETCD REST API must be used: :code:`curl http://<ip>:2379/v2/keys/smartplugs/mac_to_id/<mac-address> -XPUT -d value="<smartplug-id>"`.
   e.g. :code:`curl http://127.0.0.1:2379/v2/keys/smartplugs/mac_to_id/AC:84:C6:59:D9:A1 -XPUT -d value="sp_w.r0.c1"`.

Add devices
------------
Adding a device, means to integrate the device into the current
system. As the system is composed of modular components that are
connected by an MQTT broker, you only need to create an agent that
communicates with the device and it publishes to a specific MQTT
topic where the data is stored into InfluxDB.

For fully integrating the devices with InfluxDB and its REST API,
you must publish your devices data to the following MQTT topics:

  :code:`data/<source-name>/influx_data/<database>`


Apart from using specific topics, the published data must obey
the SenML_ format, where:

.. _SenML: https://tools.ietf.org/html/rfc8428

- BaseName+Name must be :code:`<id>/<measurement>`, e.g. :code:`sp_w.r1.c1/power`.
- The :code:`id` must obey :code:`<device-type>_<type><number>` or
  :code:`<device-type>_<location-type>.r<row-number>.c<column-number>`, e.g. :code:`sp_g3` or :code:`sp_w.r1.c1`.
- It is possible to create your own device and location types and use them on the IDs, e.g. :code:`alexa_x2` or :code:`alexa_y.r120.c902`

Some examples:

**data/sp_data/influx_data/sp**

 .. code-block:: json

    [
      {"bn":"sp_w.r1.c1/power", "u":"W","v":120.1},
    ]

**data/sp_data/influx_data/sp**

 .. code-block:: json

    [
      {"bn":"sp_", "u":"W",},
      {"n":"w.r1.c1/power","v":1.2},
      {"n":"g7/power","v":3.4}
    ]

**data/amazon_devices/influx_data/alexa**

 .. code-block:: json

    [
      {"bn":"echo-dot_", "u":"W",},
      {"n":"g2/power","v":1.2},
      {"n":"x.r1.c0/power","v":90.87},
    ]



Add API calls
--------------
We have only implemented an API for controlling the SmartPlugs
and a second API for querying data from InfluxDB.

You must realize that the REST API component does not implement
any logic for executing those requests, it is a dumb component
that takes the API call and it transforms it into a MQTT topic
and payload. So, for adding new API calls you must know how are
the API calls transformed into MQTT messages, and you have to
implement a component that listens to them, and returns an answer correctly.

Check :doc:`api <modules/api>` to understand how the REST API
calls are transformed into MQTT messages, and :doc:`MQTT <modules/mqtt>`
to understand how MQTT messages and topics must be specified.

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

.. _Fiware: https://www.fiware.org/
.. _Linksmart: https://linksmart.eu/

- :code:`query/influx_query/#`: queries to InfluxDB.
- :code:`command/sp_command/#`: commands to turn ON/OFF SmartPlugs.
- :code:`data/sp_data/influx_data/#`: data sent from SmartPlugs to InfluxDB.
- :code:`data/+/influx_data/#`: data sent from anywhere to InfluxDB.

