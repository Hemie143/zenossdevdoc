.. _zenoss_objects

*******************
Core Zenoss Objects
*******************

Most objects definitions can be found under *$ZENHOME/Products/ZenModel*.

.. _zenoss_objects_device

======
Device
======

See *$ZENHOME/Products/ZenModel/Device.py*.

.. code-block:: python

    class Device(ManagedEntity, Commandable, Lockable, MaintenanceWindowable,
                 AdministrativeRoleable, ZenMenuable):
        """
        Device is a base class that represents the idea of a single computer system
        that is made up of software running on hardware. It currently must be IP
        enabled but maybe this will change.
        """

        implements(IEventView, IIndexed, IGloballyIdentifiable)

        event_key = portal_type = meta_type = 'Device'

        default_catalog = "deviceSearch" #device ZCatalog

        relationshipManagerPathRestriction = '/Devices'
        title = ""
        manageIp = ""
        snmpAgent = ""
        snmpDescr = ""
        snmpOid = ""
        snmpContact = ""
        snmpSysName = ""
        snmpLocation = ""
        rackSlot = ""
        comments = ""
        sysedgeLicenseMode = ""
        priority = 3
        macaddresses = None


        # Flag indicating whether device is in process of creation
        _temp_device = False

        _properties = ManagedEntity._properties + (
            {'id':'title', 'type':'string', 'mode':'w'},
            {'id':'manageIp', 'type':'string', 'mode':'w'},
            {'id':'snmpAgent', 'type':'string', 'mode':'w'},
            {'id':'snmpDescr', 'type':'string', 'mode':''},
            {'id':'snmpOid', 'type':'string', 'mode':''},
            {'id':'snmpContact', 'type':'string', 'mode':''},
            {'id':'snmpSysName', 'type':'string', 'mode':''},
            {'id':'snmpLocation', 'type':'string', 'mode':''},
            {'id':'snmpLastCollection', 'type':'date', 'mode':''},
            {'id':'snmpAgent', 'type':'string', 'mode':''},
            {'id':'rackSlot', 'type':'string', 'mode':'w'},
            {'id':'comments', 'type':'text', 'mode':'w'},
            {'id':'sysedgeLicenseMode', 'type':'string', 'mode':''},
            {'id':'priority', 'type':'int', 'mode':'w'},
            )

        _relations = ManagedEntity._relations + (
            ("deviceClass", ToOne(ToManyCont, "Products.ZenModel.DeviceClass",
                "devices")),
            ("perfServer", ToOne(ToMany, "Products.ZenModel.PerformanceConf",
                "devices")),
            ("location", ToOne(ToMany, "Products.ZenModel.Location", "devices")),
            ("systems", ToMany(ToMany, "Products.ZenModel.System", "devices")),
            ("groups", ToMany(ToMany, "Products.ZenModel.DeviceGroup", "devices")),
            ("adminRoles", ToManyCont(ToOne,"Products.ZenModel.AdministrativeRole",
                "managedObject")),
            ('userCommands', ToManyCont(ToOne, 'Products.ZenModel.UserCommand',
                'commandable')),
            # unused:
            ('monitors', ToMany(ToMany, 'Products.ZenModel.StatusMonitorConf',
                'devices')),
            )

        # Screen action bindings (and tab definitions)
        factory_type_information = (
            {
                'id'             : 'Device',
                'meta_type'      : 'Device',
                'description'    : """Base class for all devices""",
                'icon'           : 'Device_icon.gif',
                'product'        : 'ZenModel',
                'factory'        : 'manage_addDevice',
                'immediate_view' : 'devicedetail',
                'actions'        :
                (
                    { 'id'           : 'swdetail'
                     , 'name'       : 'Software'
                     , 'action'     : 'deviceSoftwareDetail'
                     , 'permissions': (ZEN_VIEW, )
                    },
                    { 'id'            : 'events'
                    , 'name'          : 'Events'
                    , 'action'        : 'viewEvents'
                    , 'permissions'   : (ZEN_VIEW, )
                    },
                    { 'id'            : 'perfServer'
                    , 'name'          : 'Graphs'
                    , 'action'        : 'viewDevicePerformance'
                    , 'permissions'   : (ZEN_VIEW, )
                    },
                    { 'id'            : 'edit'
                    , 'name'          : 'Edit'
                    , 'action'        : 'editDevice'
                    , 'permissions'   : ("Change Device",)
                    },
                )
             },
            )

        security = ClassSecurityInfo()

        security.declarePrivate("propertyItems")


-----------------
Object attributes
-----------------
Inherited from Managed Entity:

* snmpindex
* monitor
* productionState
* preMWProductionState

