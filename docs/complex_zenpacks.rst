.. _complex_zenpacks

****************
Complex ZenPacks
****************

.. _complex_zenpacks_purposes

========
Purposes
========

* Support a new device type
* Poll new variables
* Display web pages with information
* Create new daemons

.. _complex_zenpacks_coding

======
Coding
======

* Logged as Zenoss user (su - zenoss)
* New device:
    * new Python object class
    * /z/ZenPacks.zenoss.dummy/ZenPacks/zenoss/dummy/MyDevice.py
    * Device class linked to Python class with *zPythonClass* (zProperty): ZenPacks.zenoss.dummy.MyDevice (dotted path)
    * Devices classes hold the monitoring configuration:
        * Monitoring templates bindings
        * Modeler plugins
        * Properties (zProperties and cProperties)
* Standard objects classes
    * Device, OSComponent, IpInterface
    * $ZENHOME/Products/ZenModel
* Relationships
* Modelers
    * $ZENHOME/Products/DataCollector/plugins/Zenoss
    * subfolders for cmd, nmap, portscan, python, snmp
    * enabled with "Modeler Plugins" under Device/Device Class page
* Templates
* GUI

    * Skin files ($ZENHOME/Products/ZenModel/skins/zenmodel)
    * Mix of HTML, CSS, TAL, AJAX, …
* Daemons
* ZenPacklib (ZPL) can shorten the development time. This is a tool designed to simplify the coding of ZenPacks.
  However, it doesn't help with coding a modeler plugin or a custom datasource.


.. _complex_zenpacks_relationships

=============
Relationships
=============

Most devices have a relationship that associates them with their interface component objects and each interface component object has a relationship back to its parent device.

* Typical objects:
    * Device
        * Object Class: *Device*
    * Component
        * Object Class: *OSComponent*, which inherits from *DeviceComponent* and *ManagedEntity*


* *ToOne* (eg, Device to DeviceClass)
* *ToMany* (eg, Device to DeviceGroup)
* *ToManyCont* (eg, Device to UserCommand)
    * ("deviceClass", ToOne(ToManyCont, "Products.ZenModel.DeviceClass", "devices"))
    * relationship name: deviceClass
    * relationship type: ToOne
    * corresponding relationship (opposite) to DeviceClass (present in DeviceClass.py)
        * relationship type: ToMany
        * Python path to object class: Products.ZenModel.DeviceClass
        * relationship name: devices
* Container Relationship

.. _complex_zenpacks_gui

===
GUI
===

* Zenoss 3: JavaScript to present device details:
* $ZENHOME/Products/ZenUI3
* $ZENHOME/Products/ZenUI3/browser/resources/js/zenoss/devdetail.js (device details)
* $ZENHOME/Products/ZenUI3/browser/resources/js/zenoss/ComponentPanel.js (component details)
* Python Object Class linked to Javascript through factory statements
    * stanza: id, name, action, permissions
    * action: link to skin file (extension .pt)
* pt file contains obsolete references for Zenoss 2 (Edit, Events, ..)






