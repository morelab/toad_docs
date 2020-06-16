===================
IoToad MQTT design
===================

In this section we will explain what is the design behind all the communications through
this technology; :ref:`how the topics are organized and constructed <Topics>`, and
:ref:`how the messages are formatted <Payload>`.

Topics
-------

There are three topics hierarchies:

* :code:`command`: channels where control commands are published. For example: switch off/on a SmartPlug
* :code:`query`: channels where data queries are published. For example: what the SmarPlugs consumption is.
* :code:`data`: channels where collected data is published. For example: a SmartPlug has consumed 5 Watts

command
________

**topic structure**: :code:`command/<module>/<subpath>`

* :code:`<module>`: it is the name of the module the message is published for.
* :code:`<subpath>`: it specifies where the message is headed inside the module. For example:
  :code:`sp_command` may have :code:`sp_command/row/<row:n>` or :code:`sp_command/<smartplug-id>` as a subpath.

**examples**

* :code:`command/sp_command/w.r1.c1`
* :code:`command/sp_command/row/1`
* :code:`command/sp_command/column/1`
* :code:`command/sp_command/m1`

query
______

**topic structure**: :code:`query/<module>/<subpath>`

* :code:`<module>`: it is the name of the module the message is published for.
* :code:`<subpath>`: it specifies where the message is headed inside the module. For example:
  :code:`influx_query` may have :code:`query/influx_query/<influx-database>/<influx-measurement>`as a subpath.

**examples**

* :code:`query/influx_query/sp/power`
* :code:`query/influx_query/sp/status`


data
_____

**topic structure**: :code:`data/<module>/<source>/<source-subpath>`

* :code:`<module>`: it is the name of the module the message is published for. The :code:`<module>` is
  the first subtopic on pourpose so that :code:`data/+/<source>/<source-subpath>` can be used when subscribing.
* :code:`<source>`: it specifies module that published the message. This is intended
  so that the topics give information about the data that is being published. For example:
  if the :code:`<source>` is :code:`sp_data`, others know that the data is related to SmarPlugs.
* :code:`<source-subpath>`: it specifies the details about the data that is being published. For example:
  :code:`sp_data` may have :code:`data/<module>/sp_data/<smartplug-id>` or
  :code:`data/<module>/sp_data/row/<row:n>` as a subpath.

**examples**

* :code:`data/influx_data/sp_data`
* :code:`data/+/sp_data`
* :code:`data/influx_data/sp_data/w.r1.c1`
* :code:`data/influx_data/sp_data/row/1`


Payload
--------

All MQTT payloads are JSONs that contain these three fields:
:code:`data`, :code:`error` and :code:`response_topic`.
MQTT payloads can have all of these fields or only one or two, depending on the specific case.

So a MQTT payload format is...

.. code-block:: json

    {
        "response_topic": "<response-topic>",
        "data": "<data>",
        "error": "<error>"
    }

* :code:`data`: this field can contain anything: a dictionary with the query parameters,
  the collected consumption from a Smartplug, etc.
* :code:`error`: this field contains a :code:`string` that is used to indicate whether
  there was an error or not. For example: this can be used for knowing if a command was executed.
* :code:`response-topic`: this field conatains a MQTT topic :code:`string`,
  which is used when a message that an answer is expected is published.
  For example whenever a query message is published, a result is expected. So the :code:`<response-topic>`
  indicates where to publish the result.

**Examples**

**SmartPlug consumption query**

:code:`query/influx_query/sp/power`

.. code-block:: json

   {
        "response_topic": "responses/api/809bd939baa44f1f87fdd1099ea05a62",
        "data": {
            "operation": "sum",
            "type": "w",
            "from": 1585217932.2041745
        }
   }

**SmartPlug consumption query reply**

:code:`responses/api/809bd939baa44f1f87fdd1099ea05a62`

.. code-block:: json

   {
        "data": [
            {"bn": "w.r1.r2", "unit":"W"},{"v": 10.4, t: 12345678},{"v": 20.1, t: 12345679}
        ]
   }


**SmartPlug status command**

:code:`command/sp_command/w.r1.c0`

.. code-block:: json

   {
        "response_topic": "responses/api/42694cca24614db48ad12f8f89be642b",
        "data": {"status" : "OFF"}
   }

**SmartPlug status command reply**

:code:`responses/api/42694cca24614db48ad12f8f89be642b`

.. code-block:: json

   {
        "error": "The SmartPlug is unreachable",
   }
