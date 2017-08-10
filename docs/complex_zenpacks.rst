Complex ZenPacks
================

Purposes
********

* Support a new device type
* Poll new variables
* Display web pages with information
* Create new daemons


Configuration data and performance data
***************************************

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


ZODB
****

* Zope Object Database
* Zenoss, based on Zope Web Application Server
* Store Python objects and their states (object-oriented database)
* information pushed by modeler plugins
* ZEO, layer between Zope and ZODB, allows multiple Zope Servers
* ZMI (Zope Management Interface) http://<zenoss server>:8080/zport/dmd/manage
* Top level = zport/dmd

    * dmd = Device Management Database

* http://docs.zope.org/zope2/zope2book/index.html

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

* lationships
* Requirement to display data about contained components

    * Skin files ($ZENHOME/Products/ZenModel/skins/zenmodel)
    * Mix of HTML, CSS, TAL, AJAX, …

Relationships
*************

* ToOne (eg, Device to DeviceClass)
* ToMany (eg, Device to DeviceGroup)
* ToManyCont (eg, Device to UserCommand)

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

* Performance data

    * Templates define datasources and data* points to collect
    * $ZENHOME/perf/Devices/<Device>/<datasource>_<datapoint>.rrd
    * Component data: subfolder under device folder (<Device>/os/interfaces/eth1)

* Configuration data

    * collected by zenmodeler
    * modeler plugins

* Event data

    * 6 tables: status (active events), history, log, detail, heartbeat & alert_status





