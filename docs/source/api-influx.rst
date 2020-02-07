===========
API influx
===========

Requirements
-------------

Queries equivalent to those of InfluxDB restricting only to:

* Consult a measurement by query
* Filter by tags only
* Use aggregators and selectors defined in `InfluxDB functions`_

.. _InfluxDB functions: https://docs.influxdata.com/influxdb/v1.7/query_language/functions/


Paths
------

* iotoad.org/api/out/influx/{database}/{measurement}?operation={operation}{tagM}={valueM}&{tagN}={valueN}

Examples
---------

* iotoad.org/api/out/influx/sp/power?type=w
* iotoad.org/api/out/influx/sp/power?operation=sum&type=w
* iotoad.org/api/out/influx/sp/power?operation=median&type=w&row=1
* iotoad.org/api/out/influx/sp/status?operation=median&type=g
* iotoad.org/api/out/influx/sp/status?operation=sum&id=sp_r1c2


OpenAPI specification
----------------------

.. raw:: html
   :file: _static/html/api-influx.html