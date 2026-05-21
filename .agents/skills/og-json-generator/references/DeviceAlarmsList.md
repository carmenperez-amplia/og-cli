# Reference: `DeviceAlarmsList`

The `DeviceAlarmsList` widget allows displaying alarms data in a table format with several features like grouping, filtering, and pagination.

You can see in ListsCommons.md the common configuration for all lists.

You have to consider this especial configuration in config:

* `Ftype`: Always must be 'alarms'
* `type`: Always must be 'DeviceAlarmsList'

### Paths in columns config

Columns path (._current.* not allowed) for DeviceAlarmsList:
* **identifier** Alarm Identifier
* **channel** Alarm Channel
* **organization** Alarm Organization
* **name** Alarm Name
* **description** Alarm Description
* **rule** Alarm Rule
* **entityIdentifier** Alarm Entity Identifier
* **resourceType** Entity Resource Type
* **subEntityIdentifier** Alarm Sub Entity Identifier
* **status** Alarm Status
* **severity** Alarm Severity
* **priority** Alarm Priority
* **openingDate** Alarm Opening Date
* **attentionDate** Alarm Attention Date
* **attentionUser** Alarm Attention User
* **attentionNote** Alarm Attention Note
* **closureDate** Alarm Closure Date
* **closureUser** Alarm Closure User
* **closureNote** Alarm Closure Note
* **extra_info** Alarm Extra Info

All datastreams must exists in datamodels, otherwise the column will not be displayed or it will throw an error.