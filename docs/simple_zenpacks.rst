.. _simple_zenpacks

***************
Simple ZenPacks
***************

.. _simple_zenpacks_create

========
Creation
========

* Created from GUI, under ADVANCED -> Settings -> ZenPacks -> Create a ZenPack. The ZenPack is automatically in development mode.
* Using the Zenoss Gui: "Add to ZenPack"
* Objects:
    * Device Classes
    * Event Classes
    * Event Mappings
    * User Commands
    * Event Commands
    * MIBs
    * Service Classes
    * Process Classes
    * Device Organizers
    * Performance Templates
    * Reports
* The objects can be removed from the ZenPack after selecting their checkboxes under *ZenPack Provides* and clicking on *Delete from ZenPack*.

.. note::

    When a ZenPack is removed, it will also remove any object it contains, including MIBs, Event Transforms, ...

* During Export, all objects under "ZenPack Provides" are written to objects.xml. The egg file is found in $ZENHOME/export


