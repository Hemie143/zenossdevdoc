.. _datasource_python

*******************************
Datasource with PythonCollector
*******************************

proxy Attributes
================

*   fetches device attributes for collection

.. code-block:: python
    class MyPlugin(PythonDataSourcePlugin):
        # List of device attributes you might need to do collection.
        proxy_attributes = (
            'zSnmpVer',
            'zSnmpCommunity',
            'zSnmpPort',
            )

__init__(self, config=None)
===========================

*   Initialize the plugin

config_key(cls, datasource, context) method
===========================================

*   run by zenhub
*   arguments

    *   datasource
    *   context: device or component

*   returns tuple with unique config id (all similar components share the same config id) to split
    batches within collector

.. code-block:: python

    @classmethod
    def config_key(cls, datasource, context):
        """
        Return list that is used to split configurations at the collector.

        This is a classmethod that is executed in zenhub. The datasource and
        context parameters are the full objects.
        """
        return (
            context.device().id,
            datasource.getCycleTime(context),
            datasource.rrdTemplate().id,
            datasource.id,
            datasource.plugin_classname,
            )

params(cls, datasource, context) method
=======================================

*   run by zenhub
*   fetches zodb data for the collection daemon
*   can collect data from datasource

    *   datasource.talesEval(datasource.snmpCommunity, context)

*   more appropriate for component config data (see proxy attributes for device)
*   returns a dictionary

.. code-block:: python

    @classmethod
    def params(cls, datasource, context):
        """
        Return params dictionary needed for this plugin.

        This is a classmethod that is executed in zenhub. The datasource and
        context parameters are the full objects.
        """
        return {}

.. code-block:: python

    @classmethod
    def params(cls, datasource, context):
        """
        Return params dictionary needed for this plugin.

        This is a classmethod that is executed in zenhub. The datasource and
        context parameters are the full objects.
        """
        params = {}
        params['snmpCommunity'] = datasource.talesEval(datasource.snmpCommunity, context)
        params['myvar'] = …
        return params

collect(self, config)
=====================

*   run by zenpython
*   to implement
*   config is instance of ZenPacks.zenoss.PythonCollector.services.PythonConfig ???
*   config.datasources is a list of ZenPacks.zenoss.PythonCollector.services.PythonConfig.PythonDataSourceConfig
    Contains a single element ?
*   config fields:

    * id: string - device hostname
    * manageIp: string - device IP address
    * _device_guid: string - device GUID
    * thresholds: list -
    * datasources: list - ZenPacks.zenoss.PythonCollector.services.PythonConfig.PythonDataSourceConfig

        * cycletime: integer - Cycle time
        * component:
        * datasource: string - Datasource name
        * template: string - Template name
        * manageIp: string - Device IP address
        * device: string - Device hostname
        * config_key: tuple - output of config_key method
        * eventClass: string - Event Class
        * eventKey: string - Event Key
        * plugin_classname: string - Plugin classname, full path dot-separated
        * points: list -
        * params: dictionary - output of params method
        * Imported properties (from proxy_attributes)

*   must return a Twisted deferred
*   deferred result sent to onResult, then onSuccess or onError

onResult(self, result, config)
==============================

*	can be used to process the result
*	return result

onSuccess(self, result, config)
===============================

*	called only on success
*	called after onResult, before onComplete
*	returns a dictionary (one dict per datapoint ???) with:

    *   {'values': defaultdict(<type 'dict'>, {}), 'events': [], 'maps':[]}

        *   events (list)
        *   values (dictionary)
        *   maps (list), can be used to modify attributes of the device or component in the ZODB

onError(self, result, config)
============================

*   called only on error
*   called after onResult, before onComplete
*   capture errors
*   optional
*   return same dictionary format as onSuccess

onComplete(self, result, config)
================================

*   called last
*   optional

cleanup(self, config)
=====================

*   called when collector exits or task is deleted
*   optional

new_data(self)
==============

*   returns an empty data structure

    *   {'values': defaultdict(<type 'dict'>, {}), 'events': [], 'maps':[]}


