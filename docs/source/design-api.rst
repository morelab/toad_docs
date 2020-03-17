=================
REST APIs design
=================

Base path of all requests:

* :code:`/api`

Division into two routes so that the separation between reading and writing resources is clear:

* :code:`api/in` to send data
* :code:`api/out` to retrieve data


api/in
+++++++

Path
_____

HTTP method: :code:`PUT`

URL: :code:`api/in/<agent>/<subpath>`

Explanation
____________

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

Format
_______

URLs
~~~~~

* :code:`api/in/sp_command/m1`
* :code:`api/in/sp_command/g0`
* :code:`api/in/sp_command/w.r1.c2`
* :code:`api/in/sp_command/row/1`
* :code:`api/in/sp_command/column/2`

PUT Data
~~~~~~~~~

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

HTTP REST request
~~~~~~~~~~~~~~~~~~
URL path: :code:`api/in/sp_command/`

Data:

.. code-block:: json

   {
       "subtopics": [
           "m1",
           "w.r1.c0"
         ],
         "payload": {"status":"OFF"}
   }

MQTT publications
~~~~~~~~~~~~~~~~~~

It will publish two times to MQTT.

**1st publication**

Topic: :code:`command/sp_command/m1`

Payload:

.. code-block:: json

   {
        "response_topic": "responses/api/809bd939baa44f1f87fdd1099ea05a62",
        "data": {"status" : "OFF"}
   }

**2nd publication**

Topic: :code:`command/sp_command/w.r1.c0`

Payload:

.. code-block:: json

   {
        "response_topic": "responses/api/42694cca24614db48ad12f8f89be642b",
        "data": {"status" : "OFF"}
   }


api/out
++++++++

Path
_____

HTTP method: :code:`GET`

*api/in/<agent>/<subpath>?<param:1>=<value:1>&<param:2>=<value:2>*

Explanation
____________

* **<agent>** is the module to which you want to send data,
  for example *sp_command/status* or *sp_command/status/sp_r3c4*.
* **<subpath>** is the subtopic of mqtt to which it will be published.
  If you want to publish to several subtopics, *<subtopic>* will be omitted
  from the URL and a “subtopics” field will be created in the data sent as
  described below.
* **<param:n>**/**<value:n>** are the parameters that... TODO


Format
_______

URLs
~~~~~

* *api/out/influx_query?*
* *api/in/sp_command/sp_g0*
* *api/in/sp_command/row/1*
* *api/in/sp_command/column/2*


Real example
_____________

HTTP REST request
~~~~~~~~~~~~~~~~~~
:code:`api/in/sp_command/`

.. code-block:: json

   {
       "subtopics": [
           "m1",
           "w.r1.c0"
         ],
         "payload": {"status":"OFF"}
   }

MQTT publications
~~~~~~~~~~~~~~~~~~

It will publish two times to MQTT:

Topic: :code:`command/sp_command/m1`
.. code-block:: json

   {
        "response_topic": "responses/api/809bd939baa44f1f87fdd1099ea05a62",
        "data": {"status" : "OFF"}
   }

and

Topic: :code:`command/sp_command/w.r1.c0`
.. code-block:: json

   {
        "response_topic": "responses/api/42694cca24614db48ad12f8f89be642b",
        "data": {"status" : "OFF"}
   }


