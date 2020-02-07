===============
APIs design
===============

Base path of all requests:

* **/api**

Division into two routes so that the separation between reading and writing resources is clear:

* **/in** to send data
* **/out** to extract data

Examples:

* **api/in**
* **api/out**

Write
++++++

Path
~~~~~

Structure
__________

*api/in/<agent>/<subpath>*

Explanation
____________

* **<agent>** is the module to which you want to send data,
  for example *sp_controller/status* or *sp_controller/status/sp_r3c4*.
* **<subpath>** is the subtopic of mqtt to which it will be published.
  If you want to publish to several subtopics, *<subtopic>* will be omitted
  from the URL and a “subtopics” field will be created in the data sent as
  described below.

Specifying to which agent to send the message prevents all other agents
from receiving it. Subpath(s) has a specific form depending on which
agent is sent so that it can adapt to the functionality of each.
For example, our agent (sp_controller) will allow you to identify
smartplugs directly by ID or filter smartplugs by row and column but
other agents may have completely different functionalities.

Examples
_________

* *api/in/sp_controller/sp_m1*
* *api/in/sp_controller/sp_g0*
* *api/in/sp_controller/row/1*
* *api/in/sp_controller/column/2*


Data
~~~~~

Structure
__________

.. code-block:: json

   {
       "subtopics": [
           "subtopic_1",
           "subtopic_2"
         ],
         "payload": "<payload>"
   }


Explanation
____________

If there are not several subtopics and the message will be sent
directly to what is specified in the URL, the list may be empty
or omitted. If not, the message will be sent to each topic consisting
of: *<agent>/<subtopics>*.


For example:

If:

* *agent*: sp_controller
* *subtopics*: sp_m1, sp_m2

Then, the payload will be sent to the following topics:

* *sp_controller/sp_m1*
* *sp_controller/sp_m2*


Examples
_________

.. code-block:: json

   {
      "subtopics": [
           "row/1",
           "row/2"
      ],
      "payload": "off"
   }

   {
      "subtopics": [],
      "payload": "on"
   }

   {
      "payload": "on"
   }




Read
+++++

