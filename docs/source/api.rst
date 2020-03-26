==========
REST APIs
==========


There are two APIs implemented for controlling and querying data from SmartPlugs:

influx_query API
-----------------
API for executing SmartPlug data queries to InfluxDB.
Even if it is intended for SmartPlugs, it is designed for querying any
data from InfluxDB. In :doc:`influx_query API <apis/influx_query>` specifies how
to construct generic queries for InfluxDB.

:doc:`influx_query API specification <apis/influx_query>`
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~


sp_command API
---------------
API for executing SmartPlug control
commands (turn ON/OFF).

:doc:`sp_command API specification <apis/sp_command>`
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
