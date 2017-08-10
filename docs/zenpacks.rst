.. _zenpacks_basics

***************
ZenPacks Basics
***************

* ZenPack = packaged Python egg (can be unzipped)
* Name: by convention= ZenPacks.<organization>.<function>
    * It's possible to have more than three segments.
    * ZenPack names must be unique
* ZenPack Metadata
    * Version
    * Author
    * License
    * Dependencies

.. _zenpacks_folder_root

=====================
Folder hierarchy Root
=====================

+-------------------------------------+---------------------------------------------------------------------------+
| Path                                | Contains                                                                  |
+=====================================+===========================================================================+
| /                                   | Object class definitions                                                  |
+-------------------------------------+---------------------------------------------------------------------------+
| /build                              | Created when ZenPack is exported to an egg file.                          |
|                                     | Can be deleted.                                                           |
+-------------------------------------+---------------------------------------------------------------------------+
| /dist                               | Created when ZenPack is exported to an egg file.                          |
|                                     | Can be deleted.                                                           |
+-------------------------------------+---------------------------------------------------------------------------+
| /ZenPacks.<organization>.<function> |	Base folder of ZenPack                                                    |
+-------------------------------------+---------------------------------------------------------------------------+
| setup.py                            | Contains parameters for use by setuptools and                             |
|                                     | distutils in creating eggs (name, author, prerequisites, license, ...).   |
+-------------------------------------+---------------------------------------------------------------------------+

.. _zenpacks_folder_base

================================================================
Folder hierarchy Base (under ZenPacks.<organization>.<function>)
================================================================

A *__init__.py* file must be present in each folder and sub-folder, even if empty.
It is recommended to remove the unwanted sample files, created together with the ZenPack.

+-------------------------------------+---------------------------------------------------------------------------+
| Path                                | Contains                                                                  |
+=====================================+===========================================================================+
| __init__.py                         | Code executed when ZenPack is loaded                                      |
+-------------------------------------+---------------------------------------------------------------------------+
| analytics.py                        | Integration with Service Dynamics Analytics                               |
+-------------------------------------+---------------------------------------------------------------------------+
| configure.zxml                      | Glue to link info & interface classes with GUI code                       |
+-------------------------------------+---------------------------------------------------------------------------+
| dynamicview.py                      | Integration with Service Dynamics Dynamic Views                           |
+-------------------------------------+---------------------------------------------------------------------------+
| events.py                           | Integration with Service Dynamics Impact                                  |
+-------------------------------------+---------------------------------------------------------------------------+
| ExampleComponent.py                 | Sample code for a new Component                                           |
+-------------------------------------+---------------------------------------------------------------------------+
| ExampleDevice.py                    | Sample code for a new Device                                              |
+-------------------------------------+---------------------------------------------------------------------------+
| info.py                             | Mapping between object attributes and interface classes for GUI           |
+-------------------------------------+---------------------------------------------------------------------------+
| interfaces.py                       | Definition of attributes to show in GUI                                   |
+-------------------------------------+---------------------------------------------------------------------------+
| LICENSE.txt                         | License text                                                              |
+-------------------------------------+---------------------------------------------------------------------------+
| zenexample.py                       | Sample code for daemon                                                    |
+-------------------------------------+---------------------------------------------------------------------------+
| /bin                                | Binaries created by the ZenPack                                           |
+-------------------------------------+---------------------------------------------------------------------------+
| /browser                            | GUI definitions                                                           |
+-------------------------------------+---------------------------------------------------------------------------+
| /daemons                            | Daemons                                                                   |
+-------------------------------------+---------------------------------------------------------------------------+
| /datasources                        | Datasources                                                               |
+-------------------------------------+---------------------------------------------------------------------------+
| /lib                                | 3rd party modules, ZenPack depends on                                     |
+-------------------------------------+---------------------------------------------------------------------------+
| /libexec                            | scripts and binaries used by the ZenPack                                  |
+-------------------------------------+---------------------------------------------------------------------------+
| /migrate                            | Migrating code between Zenoss versions                                    |
+-------------------------------------+---------------------------------------------------------------------------+
| /modeler/plugins                    | Modeler plugins for object classes                                        |
+-------------------------------------+---------------------------------------------------------------------------+
| /objects                            | Database objects (device classes, templates, …) added via GUI             |
+-------------------------------------+---------------------------------------------------------------------------+
| /parsers                            | Command parsers                                                           |
+-------------------------------------+---------------------------------------------------------------------------+
| /reports                            | Report plugins                                                            |
+-------------------------------------+---------------------------------------------------------------------------+
| /services                           | Configuration services for custom daemons                                 |
+-------------------------------------+---------------------------------------------------------------------------+
| /tests                              | Test scripts                                                              |
+-------------------------------------+---------------------------------------------------------------------------+
| /zep                                | Custom triggers, notifications and event fields                           |
+-------------------------------------+---------------------------------------------------------------------------+

.. _zenpacks_export

======
Export
======

* Export ZenPack to a different system
* Create object/object.xml
* During Export, all objects under "ZenPack Provides" are written to objects.xml

.. _zenpacks_install

=======
Install
=======

* zenpack --install <ZenPackName>
* zenoss restart
* zenhub restart & zopectl restart (zenwebserver restart)

.. _zenpacks_setuptools

==========
setuptools
==========

* Zenoss provides zenpacksupport, subclass of setuptools
* zenpacksupport defines additional metadata in eggs

  * compatZenossVers: required Zenoss version
  * preZenPackName: non-egg ZenPack that is to be replaced

.. _zenpacks_data

=========================================
Configuration, performance and event data
=========================================

.. image:: /_pictures/db_daemons.png

* Configuration data:

    * polled every 12 hours,
    * stored in ZODB,
    * definitions in modeler plugins
    * polled by zenmodeler, using modeler plugins (collector plugins)

* Performance data:

    * polled every 5 minutes,
    * stored in RRD files
      $ZENHOME/perf/Devices/<Device>/<Component>/<datasource>_<datapoint>.rrd
    * specifications in templates (contains datasources and datapoints definitions)
    * performed by daemons:

        * *zenperfsnmp* for SNMP data
        * *zencommand* for SSH data
        * *zenpython*
        * *zenprocess* for discovered processes

* Event data

    * 6 tables: status (active events), history, log, detail, heartbeat & alert_status


.. _zenpacks_zodb

====
ZODB
====

* Zope Object Database
* Zenoss, based on Zope Web Application Server
* Store Python objects and their states (object-oriented database)
* Store information pushed by modeler plugins
* ZEO, layer between Zope and ZODB, allows multiple Zope Servers
* ZMI (Zope Management Interface) http://<zenoss server>:8080/zport/dmd/manage
* Top level = zport/dmd

    * dmd = Device Management Database

* http://docs.zope.org/zope2/zope2book/index.html

