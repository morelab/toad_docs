=======================
Rest API: Influx query
=======================

API for executing SmartPlug data queries to InfluxDB.
Even if it is intended for SmartPlugs, it is designed for querying any
data from InfluxDB. In :doc:`influx_query API <apis/influx_query>` specifies how
to construct generic queries for InfluxDB.

Requirements
-------------

This API is a middleware inbetween you and Influx. It queries InfluxDB and returns the results formatted as a SenML.
For a deeper understanding check information about `InfluxDB <https://v2.docs.influxdata.com/v2.0/>`_ and
`SenML <https://tools.ietf.org/html/rfc8428>`_.

The API let you construct queries equivalent to those of InfluxDB restricted to:

* Query a single measurement per query
* Filter by tags
* Use aggregator and selectors defined in
  `InfluxDB functions <https://docs.influxdata.com/influxdb/v1.7/query_language/functions/>`_


URL
----

* :code:`iotoad.org/api/out/influx_query/{database}/{measurement}?operation={operation}{tagM}={valueM}&{tagN}={valueN}`

Examples
---------

* :code:`iotoad.org/api/out/influx_query/sp/power?type=w`
* :code:`iotoad.org/api/out/influx_query/sp/power?operation=sum&type=w&from=1585217932.2041745`
* :code:`iotoad.org/api/out/influx_query/sp/power?operation=median&type=w&row=1&from=1585217932.2041745&to=1585300000.2041745`
* :code:`iotoad.org/api/out/influx_query/sp/status?operation=median&type=g`
* :code:`iotoad.org/api/out/influx_query/sp/status?operation=sum&id=w.r1.c2`


OpenAPI specification
----------------------

.. raw:: html
   :file: ../_static/html/api-influx_query.html