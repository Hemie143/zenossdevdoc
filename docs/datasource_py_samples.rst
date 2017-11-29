.. _datasource_python

*******************************
Datasource with PythonCollector
*******************************

Skeleton (no protocol)
======================

.. code-block:: python

    import time

    from Products.ZenEvents import ZenEventClasses

    from ZenPacks.zenoss.PythonCollector.datasources.PythonDataSource \
        import PythonDataSourcePlugin

    class MyPlugin(PythonDataSourcePlugin):
        """Explanation of what MyPlugin does."""

        # List of device attributes you'll need to do collection.
        proxy_attributes = (
            'zCommandUsername',
            'zCommandPassword',
            )

        @classmethod
        def config_key(cls, datasource, context):
            """
            Return a tuple defining collection uniqueness.
            This is a classmethod that is executed in zenhub. The datasource and
            context parameters are the full objects.
            This example implementation is the default. Split configurations by
            device, cycle time, template id, datasource id and the Python data
            source's plugin class name.
            You can omit this method from your implementation entirely if this
            default uniqueness behavior fits your needs. In many cases it will.
            """
            return (
                context.device().id,
                datasource.getCycleTime(context),
                datasource.rrdTemplate().id,
                datasource.id,
                datasource.plugin_classname,
                )

        @classmethod
        def params(cls, datasource, context):
            """
            Return params dictionary needed for this plugin.
            This is a classmethod that is executed in zenhub. The datasource and
            context parameters are the full objects.
            This example implementation will provide no extra information for
            each data source to the collect method.
            You can omit this method from your implementation if you don't require
            any additional information on each of the datasources of the config
            parameter to the collect method below. If you only need extra
            information at the device level it is easier to just use
            proxy_attributes as mentioned above.
            """
            return {}

        def collect(self, config):
            """
            No default collect behavior. You must implement this method.
            This method must return a Twisted deferred. The deferred results will
            be sent to the onResult then either onSuccess or onError callbacks
            below.
            """
            ds0 = config.datasources[0]
            return somethingThatReturnsADeferred(
                username=ds0.zCommandUsername,
                password=ds0.zCommandPassword)

        def onResult(self, result, config):
            """
            Called first for success and error.
            You can omit this method if you want the result of the collect method
            to be used without further processing.
            """
            return result

        def onSuccess(self, result, config):
            """
            Called only on success. After onResult, before onComplete.
            You should return a data structure with zero or more events, values
            and maps.
            """
            collectionTime = time.time()
            return {
                'events': [{
                    'summary': 'successful collection',
                    'eventKey': 'myPlugin_result',
                    'severity': ZenEventClasses.Clear,
                    },{
                    'summary': 'first event summary',
                    'eventKey': 'myPlugin_result',
                    'severity': ZenEventClasses.Info,
                    },{
                    'summary': 'second event summary',
                    'eventKey': 'myPlugin_result',
                    'severity': ZenEventClasses.Warning,
                    }],

                'values': {
                    None: {
                        # datapoints for the device (no component)
                        'datasource1_datapoint1': (123.4, collectionTime),
                        'datasource1_datapoint2': (5.678, collectionTime),
                        },
                    'cpu1': {
                        # datapoints can be specified per datasource...
                        'datasource1_user': (12.1, collectionTime),
                        'datasource2_user': (13.2, collectionTime),
                        # or just by id
                        'datasource1_system': (1.21, collectionTime),
                        'io': (23, collectionTime),
                        }
                    },

                'maps': [
                    ObjectMap(...),
                    RelationshipMap(..),
                    ],

                # Optional attribute, in most cases it's used when you want to change
                # the execution interval of a task during the data collection.
                'interval': 300,

                }

        def onError(self, result, config):
            """
            Called only on error. After onResult, before onComplete.
            You can omit this method if you want the error result of the collect
            method to be used without further processing. It recommended to
            implement this method to capture errors.
            """
            return {
                'events': [{
                    'summary': 'error: %s' % result,
                    'eventKey': 'myPlugin_result',
                    'severity': 4,
                    }],
                }

        def onComplete(self, result, config):
            """
            Called last for success and error.
            You can omit this method if you want the result of either the
            onSuccess or onError method to be used without further processing.
            """
            return result

        def cleanup(self, config):
            """
            Called when collector exits, or task is deleted or changed.
            """
        return

Weather Underground - HTTP API
==============================
Implement events, datapoints and modeling

.. code-block:: python

    # Logging
    import logging
    LOG = logging.getLogger('zen.WeatherUnderground')

    # stdlib Imports
    import json
    import time

    # Twisted Imports
    from twisted.internet.defer import inlineCallbacks, returnValue
    from twisted.web.client import getPage

    # PythonCollector Imports
    from Products.DataCollector.plugins.DataMaps import ObjectMap
    from ZenPacks.zenoss.PythonCollector.datasources.PythonDataSource import PythonDataSourcePlugin


    class Alerts(PythonDataSourcePlugin):

        """Weather Underground alerts data source plugin."""

        @classmethod
        def config_key(cls, datasource, context):
            return (
                context.device().id,
                datasource.getCycleTime(context),
                context.id,
                'wunderground-alerts',
                )

        @classmethod
        def params(cls, datasource, context):
            return {
                'api_key': context.zWundergroundAPIKey,
                'api_link': context.api_link,
                'location_name': context.title,
                }

        @inlineCallbacks
        def collect(self, config):
            data = self.new_data()

            for datasource in config.datasources:
                try:
                    response = yield getPage(
                        'http://api.wunderground.com/api/{api_key}/alerts{api_link}.json'
                        .format(
                            api_key=datasource.params['api_key'],
                            api_link=datasource.params['api_link']))

                    response = json.loads(response)
                except Exception:
                    LOG.exception(
                        "%s: failed to get alerts data for %s",
                        config.id,
                        datasource.location_name)

                    continue

                for alert in response['alerts']:
                    severity = None

                    if int(alert['expires_epoch']) <= time.time():
                        severity = 0
                    elif alert['significance'] in ('W', 'A'):
                        severity = 3
                    else:
                        severity = 2

                    data['events'].append({
                        'device': config.id,
                        'component': datasource.component,
                        'severity': severity,
                        'eventKey': 'wu-alert-{}'.format(alert['type']),
                        'eventClassKey': 'wu-alert',

                        'summary': alert['description'],
                        'message': alert['message'],

                        'wu-description': alert['description'],
                        'wu-date': alert['date'],
                        'wu-expires': alert['expires'],
                        'wu-phenomena': alert['phenomena'],
                        'wu-significance': alert['significance'],
                        'wu-type': alert['type'],
                        })

            returnValue(data)


    class Conditions(PythonDataSourcePlugin):

        """Weather Underground conditions data source plugin."""

        @classmethod
        def config_key(cls, datasource, context):
            return (
                context.device().id,
                datasource.getCycleTime(context),
                context.id,
                'wunderground-conditions',
                )

        @classmethod
        def params(cls, datasource, context):
            return {
                'api_key': context.zWundergroundAPIKey,
                'api_link': context.api_link,
                'location_name': context.title,
                }

        @inlineCallbacks
        def collect(self, config):
            data = self.new_data()

            for datasource in config.datasources:
                try:
                    response = yield getPage(
                        'http://api.wunderground.com/api/{api_key}/conditions{api_link}.json'
                        .format(
                            api_key=datasource.params['api_key'],
                            api_link=datasource.params['api_link']))

                    response = json.loads(response)
                except Exception:
                    LOG.exception(
                        "%s: failed to get conditions data for %s",
                        config.id,
                        datasource.location_name)

                    continue

                current_observation = response['current_observation']
                for datapoint_id in (x.id for x in datasource.points):
                    if datapoint_id not in current_observation:
                        continue

                    try:
                        value = current_observation[datapoint_id]
                        if isinstance(value, basestring):
                            value = value.strip(' %')

                        value = float(value)
                    except (TypeError, ValueError):
                        # Sometimes values are NA or not available.
                        continue

                    dpname = '_'.join((datasource.datasource, datapoint_id))
                    data['values'][datasource.component][dpname] = (value, 'N')

                data['maps'].append(
                    ObjectMap({
                        'relname': 'wundergroundLocations',
                        'modname': 'ZenPacks.training.WeatherUnderground.WundergroundLocation',
                        'id': datasource.component,
                        'weather': current_observation['weather'],
                        }))

            returnValue(data)

BMC device - Process
====================

See the excellent posts of Andrés Álvarez here :
http://aalvarez.me/blog/posts/working-with-zenoss-python-data-sources.html

.. code-block:: python

    # Logging
    import logging
    log = logging.getLogger('zen.MyZenPack')

    # Twisted Imports
    from twisted.internet.defer import inlineCallbacks, returnValue

    # PythonCollector Imports
    from Products.DataCollector.plugins.DataMaps import ObjectMap
    from ZenPacks.zenoss.PythonCollector.datasources.PythonDataSource import (
         PythonDataSourcePlugin,
         )

    import subprocess

    class BmcPowerStatus(PythonDataSourcePlugin):
        """BMC power status data source plugin."""

        # List of device attributes needed for collection
        proxy_attribures = (
            'zBmcAddress',
            'zIpmiUsername',
            'zIpmiPassword',
        )

        @classmethod
        def config_key(cls, datasource, context):
            return (
                context.device().id,
                datasource.getCycleTime(context),
                context.id,
                'myzenpack-powerstatus',
            )

        @classmethod
        def params(cls, datasource, context):
            return {
                'zBmcAddress': context.zBmcAddress,
                'zIpmiUsername': context.zIpmiUsername,
                'zIpmiPassword': context.zIpmiPassword,
                }

        @inlineCallbacks
        def collect(self, config):
            log.debug("Collect for BMC Power Status ({0})".format(config.id))

            ds0 = config.datasources[0]
            results = {}

            # Collect using ipmitool
            power_status = False
            cmd_result = ''
            try:
                cmd = 'ipmitool -H {0} -I lanplus -U {1} -P {2} power status'.format(ds0.zBmcAddress, ds0.zIpmiUsername, ds0.zIpmiPassword)
                cmd_result = yield subprocess.check_output(cmd, shell=True).rstrip()
                log.info('Power Status for Device {0}: {1}'.format(ds0.zBmcAddress, cmd_result))
            except:
                log.error('Error when running ipmitool when collecting Power Status on BMC Address {0}'.format(ds0.zBmcAddress))

            if cmd_result == 'Chassis Power is on':
                power_status = True

            results['power_status'] = power_status

            returnValue(results)

        def onSuccess(self, result, config):
            data = self.new_data()

            power_status = result['power_status']

            data['maps'].append(
                ObjectMap({
                    'modname': 'ZenPacks.itri.BmcMonitor.BmcServer',
                    'power_status': power_status,
                    }))

            if power_status:
                data['events'].append({
                    'device': config.id,
                    'summary': '{0} BMC power status is now UP'.format(config.id),
                    'severity': ZenEventClasses.Clear,
                    'eventClassKey': 'bmcPowerStatus',
                    })
            else:
                data['events'].append({
                    'device': config.id,
                    'summary': '{0} BMC power status is DOWN!'.format(config.id),
                    'severity': ZenEventClasses.Critical,
                    'eventClassKey': 'bmcPowerStatus',
                    })

            data['events'].append({
                'device': config.id,
                'summary': 'BMC Power Status Collector: successful collection',
                'severity': ZenEventClasses.Clear,
                'eventKey': 'bmcPowerStatusCollectionError',
                'eventClassKey': 'bmcMonitorFailure',
                })

            return data

        def onError(self, result, config):
            errmsg = 'BMC Power Status Collector: Error trying to collect.'
            log.error('{0}: {1}'.format(config.id, errmsg))

            data = self.new_data()

            data['events'].append({
                'device': config.id,
                'summary': errmsg,
                'severity': ZenEventClasses.Critical,
                'eventKey': 'bmcPowerStatusCollectionError',
                'eventClassKey': 'bmcMonitorFailure',
                })

            return data

