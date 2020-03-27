========
Modules
========

MQTT
-----
This is not a module, but a publish/subscribe message broker technology. Nevertheless it is important to know
what is the design behind all the communications through this technology; how the topics
are organized and constructed, and how the messages are formatted.

:doc:`More details <modules/mqtt>`
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

api
----
This module is a REST HTTP gateway for communicating with the IoToad system.
Whenever you want to :code:`query` data or send a :code:`command`, you do it through this module.
It is a dumb module that its only logic is:

#. Listen for HTTP REST requests
#. Parse the request, and send it to MQTT so that other module
   (such as :code:`influx_query` or :code:`sp_command`) handles the query/command
#. Wait for the reply listening on a MQTT
#. Send back the reply to the HTTP request sender


.. image:: _static/imgs/flow-api.svg


:doc:`More details <modules/api>`
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~


influx_data
------------


[Modules code]