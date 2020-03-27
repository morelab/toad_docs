====================
api module's design
====================

This module listens to HTTP REST requests, and relays them into MQTT,
so that other modules can handle and respond to them, and after they handle it, :code:`api` module
listens in MQTT for the response and it sends it back to the HTTP requester.

In this section we will explain what is the design behind the HTTP REST requests and MQTT messages.

HTTP REST
----------

Base path for REST HTTP requests:

* :code:`/api`

Division into two routes so that the separation between reading and writing resources is clear:

* :ref:`api/in` to write/send data
* :ref:`api/out` to read/retrieve data


api/in
-------

REST
_____

**HTTP method**: :code:`PUT`

**URL**: :code:`api/in/<agent>/<subpath>`

* :code:`<agent>`: is the module to which you want to send data.
  For example :code:`sp_command/status` or :code:`sp_command/status/w.r3.c4`.
* :code:`<subpath>`: is the subtopic of mqtt to which it will be published.
  This is specific for each module. That is, the :code:`<subpath>` for :code:`sp_command`
  is different from any other.
  If you want to publish to several subtopics, a :code:`subtopics` field can be
  included in the data sent as described below.

Specifying to which agent to send the message prevents all other agents
from receiving it. Subpath(s) has a specific form depending on which
agent is sent so that it can adapt to the functionality of each.
For example, our agent (sp_command) will allow you to identify
smartplugs directly by ID or filter smartplugs by row and column but
other agents may have completely different functionalities.

**URL Examples**:

* :code:`api/in/sp_command/m1`
* :code:`api/in/sp_command/g0`
* :code:`api/in/sp_command/w.r1.c2`
* :code:`api/in/sp_command/row/1`
* :code:`api/in/sp_command/column/2`

**PUT Data**:

.. code-block:: json

   {
       "subtopics": [
           "<subtopic:1>",
           "<subtopic:2>"
         ],
         "payload": "<payload>"
   }


**or**

.. code-block:: json

   {
        "payload": "<payload>"
   }


If there are not several subtopics and the message will be sent
directly to what is specified in the URL, the list may be empty
or omitted. If not, the message will be sent to each topic consisting
of: :code:`<agent>/<subpath>/<subtopic>`.


MQTT
_____

In order to understand how MQTT messages are formatted in IoToad check: :doc:`IoToad MQTT design <mqtt>`.

:code:`api` parses the REST message and relays them to other modules through MQTT messages.

The MQTT messages have two fields :code:`data` and :code:`response_topic`. In the data field is
sent the :code:`payload` and the :code:`response_topic` is a random MQTT topic where :code:`api` module
listens for the message reply.

A MQTT message is sent for every topic in the :code:`subtopics`.

**Example 1: with 'subtopics'**

If the PUT data is...

.. code-block:: json

   {
       "subtopics": [
           "<subtopic:1>",
           "<subtopic:2>"
         ],
         "payload": "<payload>"
   }

Will publish two MQTT messages...

**1st message**

:code:`<agent>/<subpath>/<subtopic:1>`

.. code-block:: json

    {
        "response_topic": "<response-topic:1>"
        "data": "<payload>"
    }

**2nd message**

:code:`<agent>/<subpath>/<subtopic:2>`

.. code-block:: json

    {
        "response_topic": "<response-topic:2>"
        "data": "<payload>"
    }


**Example 2: without 'subtopics'**

If the PUT data is...

.. code-block:: json

    {
        "payload": "<payload>"
    }

Will publish a single MQTT message...

:code:`<agent>/<subpath>`

.. code-block:: json

    {
        "response_topic": "<response-topic>"
        "data": "<payload>"
    }


Real example
_____________

**HTTP REST request**

PUT :code:`api/in/sp_command/`

.. code-block:: json

   {
       "subtopics": [
           "m1",
           "w.r1.c0"
         ],
         "payload": {"status":"OFF"}
   }

**MQTT publications**

It will publish two times to MQTT.

**1st publication**

:code:`command/sp_command/m1`

.. code-block:: json

   {
        "response_topic": "responses/api/809bd939baa44f1f87fdd1099ea05a62",
        "data": {"status" : "OFF"}
   }

**2nd publication**

:code:`command/sp_command/w.r1.c0`

.. code-block:: json

   {
        "response_topic": "responses/api/42694cca24614db48ad12f8f89be642b",
        "data": {"status" : "OFF"}
   }


api/out
--------

REST
_____

**HTTP method**: :code:`GET`

**URL**: :code:`api/in/<agent>/<subpath>?<param:1>=<value:1>&<param:2>=<value:2>`

* :code:`<agent>` is the module to which you want to send data,
  for example *sp_command/status* or *sp_command/status/sp_r3c4*.
* :code:`<subpath>` is the subtopic of mqtt to which it will be published.
  If you want to publish to several subtopics, *<subtopic>* will be omitted
  from the URL and a “subtopics” field will be created in the data sent as
  described below.
* :code:`<param:n>`/:code:`<value:n>` are the parameters that specify the query.


**URL examples**

* :code:`api/out/influx_query/sp/power?type=w`
* :code:`api/out/influx_query/sp/power?operation=sum&type=w&from=1585217932.2041745`
* :code:`api/out/influx_query/sp/power?operation=median&type=w&row=1&from=1585217932.2041745&to=1585300000.2041745`
* :code:`api/out/influx_query/sp/status?operation=median&type=g`
* :code:`api/out/influx_query/sp/status?operation=sum&id=w.r1.c2`


MQTT
_____

In order to understand how MQTT messages are formatted in IoToad check: :doc:`IoToad MQTT design <mqtt>`.

The MQTT messages have two fields :code:`data` and :code:`response_topic`. In the :code:`data` field are
sent all the GET parameters as a json, and the :code:`response_topic` is a random MQTT topic where :code:`api` module
listens for the message reply.

**Example**

If the GET request is...

:code:`api/in/<agent>/<subpath>?<param:1>=<value:1>&<param:2>=<value:2>`

Will publish a single MQTT message...

:code:`<agent>/<subpath>`

.. code-block:: json

    {
        "response_topic": "<response-topic>"
        "data": {
            "<param:1>": "<value:1>",
            "<param:2>": "<value:2>"
        }
    }


Real example
_____________

**HTTP REST request**

GET :code:`api/out/influx_query/sp/power?operation=sum&type=w&from=1585217932.2041745`

**MQTT publication**

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
