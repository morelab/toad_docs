=================
API influx_query
=================

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
* :code:`iotoad.org/api/out/influx_query/sp/power?operation=sum&type=w`
* :code:`iotoad.org/api/out/influx_query/sp/power?operation=median&type=w&row=1`
* :code:`iotoad.org/api/out/influx_query/sp/status?operation=median&type=g`
* :code:`iotoad.org/api/out/influx_query/sp/status?operation=sum&id=sp_r1c2`


OpenAPI specification
----------------------

.. raw:: html
   :file: ../_static/html/api-influx_query.html