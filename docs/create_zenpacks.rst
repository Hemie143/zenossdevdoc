.. _create_zenpacks

*****************
ZenPacks Creation
*****************

.. _create_zenpacks_prep

===========
Preparation
===========

* Define names

    * ZenPack name: ZenPacks.<organization>.<function>
    * ObjectClass names
    * Relationship names

        * distinct from ObjectClass names

.. _create_zenpacks_create

====================
Creating the ZenPack
====================

* Create from GUI*
    * ADVANCED -> Settings -> ZenPacks, Action->Create a ZenPack
    * Define version, dependencies and author
    * cp -r $ ZENHOME/ZenPacks/<ZenPackName> /z/
    * zenpack --link --install /z/<ZenPackName>
    This places a single link file under $ZENHOME/ZenPacks

.. _create_zenpacks_el_gui

=================
Elements from GUI
=================

* Device Class
* MIBs
* Monitoring templates

.. _create_zenpacks_el

==============
Other elements
==============

* Object Classes
    * One file per device and per component
    * Links by matching pair of relationships
    * Files in base directory
* Modeler plugins
* UI Javascript

.. _create_zenpacks_links

=====
Links
=====

* Link Device Class with Device Object Class
    * zProperty zPythonClass
    * value = <ZenPackName>.<DeviceObjectClass>
* Link Device Class with modeler plugins
    * Device Class -> Modeler plugins

.. _create_zenpacks_egg

========
Egg file
========
* Created from subfolder build/lib into dist folder and finally copied to $ZENHOME/export
* Generation together with objects.xml
* Create egg without updating objects.xml
cd <ZenPackFolder>
python setup.py bdist_egg

.. _create_zenpacks_migrate

=========
Migration
=========
* Subclass ZenModel.ZenPack.ZenPackMigration

.. _create_zenpacks_egg2dev

===============================
Convert egg to Development Mode
===============================
1.	Install egg as usual.
2.	Restart Zenoss.
3.	Copy the ZenPack development files into egg's folder
cp $ZENHOME/Products/ZenModel/ZenPackTemplate/* $ZENHOME/ZenPacks/ZenPacks.community.YourZenPack-1.0.2-py2.4.egg/
4.	Edit ZenPack.
5.	Export ZenPack

.. _create_zenpacks_template

================
ZenPack Template
================

::

    bin/placeholder.txt
    browser/configure.zcml
    browser/resources/css/placeholder.txt
    browser/resources/img/placeholder.txt
    browser/resources/js/ExampleDevice.js
    browser/__init__.py
    daemons/zenexample
    datasources/ExampleDataSource.py.example
    datasources/__init__.py
    lib/__init__.py
    libexec/placeholder.txt
    migrate/ExampleMigration.py
    migrate/__init__.py
    modeler/plugins/community/cmd/ExampleCMD.py.example
    modeler/plugins/community/cmd/__init__.py
    modeler/plugins/community/snmp/ExampleSNMP.py.example
    modeler/plugins/community/snmp/__init__.py
    modeler/plugins/community/__init__.py
    modeler/plugins/__init__.py
    modeler/__init__.py
    reports/Example_Reports/Example_Report.rpt.example
    reports/plugins/example_plugin.py
    services/ExampleConfigService.py
    services/__init__.py
    tests/testExample.py
    tests/__init__.py
    zep/actions.json.example
    zep/zep.json.example
    __init__.py
    analytics.py
    configure.zcml
    dynamicview.py
    events.py
    ExampleComponent.py
    ExampleDevice.py
    impact.py
    info.py
    interfaces.py
    zenexample.py
