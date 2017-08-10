Complex ZenPacks
================

Purposes
********

* Support a new device type
* Poll new variables
* Display web pages with information
* Create new daemons


Coding
******

* Logged as Zenoss user (su - zenoss)
* New device:

    * new Python object class
    * /z/ZenPacks.zenoss.dummy/ZenPacks/zenoss/dummy/MyDevice.py
    * Device class linked to Python class with zPythonClass (zProperty): ZenPacks.zenoss.dummy.MyDevice (dotted path)

* Standard objects classes

    * Device, OSComponent, IpInterface
    * $ZENHOME/Products/ZenModel

* Relationships
* GUI

    * Skin files ($ZENHOME/Products/ZenModel/skins/zenmodel)
    * Mix of HTML, CSS, TAL, AJAX, …

* Daemons

Relationships
*************

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

GUI
***

* Zenoss 3: JavaScript to present device details:
* $ZENHOME/Products/ZenUI3
* $ZENHOME/Products/ZenUI3/browser/resources/js/zenoss/devdetail.js (device details)
* $ZENHOME/Products/ZenUI3/browser/resources/js/zenoss/ComponentPanel.js (component details)
* Python Object Class linked to Javascript through factory statements

    * stanza: id, name, action, permissions
    * action: link to skin file (extension .pt)

* pt file contains obsolete references for Zenoss 2 (Edit, Events, ..)

Database, Daemons and Directories
*********************************

.. image:: /_pictures/db_daemons.png

* Performance data

    * Templates define datasources and data* points to collect
    * $ZENHOME/perf/Devices/<Device>/<datasource>_<datapoint>.rrd
    * Component data: subfolder under device folder (<Device>/os/interfaces/eth1)

* Configuration data

    * collected by zenmodeler
    * modeler plugins

* Event data

    * 6 tables: status (active events), history, log, detail, heartbeat & alert_status





