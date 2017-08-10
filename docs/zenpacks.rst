.. _zenpacks_basics

***************
ZenPacks Basics
***************

* ZenPack = packaged Python egg

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
|                                     | distutils in creating eggs.                                               |
+-------------------------------------+---------------------------------------------------------------------------+

.. _zenpacks_folder_base

================================================================
Folder hierarchy Base (under ZenPacks.<organization>.<function>)
================================================================

+-------------------------------------+---------------------------------------------------------------------------+
| Path                                | Contains                                                                  |
+=====================================+===========================================================================+
| __init__.py                         | Code executed when ZenPack is loaded                                      |
+-------------------------------------+---------------------------------------------------------------------------+
| /bin                                |                                                                           |
+-------------------------------------+---------------------------------------------------------------------------+
| /browser                            |                                                                           |
+-------------------------------------+---------------------------------------------------------------------------+
| /daemons                            | Daemons                                                                   |
+-------------------------------------+---------------------------------------------------------------------------+
| /datasources                        | Datasources                                                               |
+-------------------------------------+---------------------------------------------------------------------------+
| /lib                                | 3rd party modules, ZenPack depends on                                     |
+-------------------------------------+---------------------------------------------------------------------------+
| /libexec                            | plugins (Nagios- or Cacti styles)                                         |
+-------------------------------------+---------------------------------------------------------------------------+
| /migrate                            | Migrating code between versions                                           |
+-------------------------------------+---------------------------------------------------------------------------+
| /modeler/plugins                    | Modeler plugins for object classes                                        |
+-------------------------------------+---------------------------------------------------------------------------+
| /objects                            | Database objects (device classes, templates, …) added via GUI             |
+-------------------------------------+---------------------------------------------------------------------------+
| /parsers                            | Command parsers                                                           |
+-------------------------------------+---------------------------------------------------------------------------+
| /reports                            | Report plugins                                                            |
+-------------------------------------+---------------------------------------------------------------------------+
| /skins/<ZenPackName>                | Skin files, web page templates                                            |
+-------------------------------------+---------------------------------------------------------------------------+
| /services                           |                                                                           |
+-------------------------------------+---------------------------------------------------------------------------+
| /tests                              |                                                                           |
+-------------------------------------+---------------------------------------------------------------------------+
| /zep                                |                                                                           |
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

=======================================
Configuration data and performance data
=======================================

* Configuration data:

    * polled every 12 hours,
    * stored in ZODB,
    * polled by zenmodeler, using modeler plugins (collector plugins)

* Performance data:

    * polled every 5 minutes,
    * stored in RRD files
    * specifications in templates

* Modelers

    * $ZENHOME/Products/DataCollector/plugins/Zenoss
    * subfolders for cmd, nmap, portscan, python, snmp
    * enabled with "Modeler Plugins" under Device/Device Class page

* Templates

    * SNMP data collected by zenperfsnmp daemon
    * SSH data collected by zencommand daemon

.. _zenpacks_zodb

====
ZODB
====

* Zope Object Database
* Zenoss, based on Zope Web Application Server
* Store Python objects and their states (object-oriented database)
* information pushed by modeler plugins
* ZEO, layer between Zope and ZODB, allows multiple Zope Servers
* ZMI (Zope Management Interface) http://<zenoss server>:8080/zport/dmd/manage
* Top level = zport/dmd

    * dmd = Device Management Database

* http://docs.zope.org/zope2/zope2book/index.html

