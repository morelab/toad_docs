====================
api module's design
====================



Base path for REST HTTP requests:

* :code:`/api`

Division into two routes so that the separation between reading and writing resources is clear:

* :ref:`api/in` to write/send data
* :ref:`api/out` to read/retrieve data


api/in
-------

**HTTP method**: :code:`PUT`

**URL**: :code:`api/in/<agent>/<subpath>`

* :code:`<agent>`: is the module to which you want to send data.
  For example :code:`sp_command/status` or :code:`sp_command/status/w.r3.c4`.
* :code:`<subpath>`: is the subtopic of mqtt to which it will be published.
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
of: :code:`<agent>/<subtopic>`.


Real example
_____________

**HTTP REST request**

GET :code:`api/in/sp_command/`

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

**HTTP method**: :code:`GET`

**URL** :code:`api/in/<agent>/<subpath>?<param:1>=<value:1>&<param:2>=<value:2>`

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


Real example
_____________

**HTTP REST request**

PUT :code:`api/out/influx_query/sp/power?operation=sum&type=w&from=1585217932.2041745`

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
