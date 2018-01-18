Internal messages
=================

There are some messages that are published by DeviceManager through Kafka.
These messages are notifications of device management operations, and they can
be consumed by any component interested in them, such as IoT agents.

.. list-table:: Kafka messages
   :header-rows: 1

   * - Event
     - Service
     - Message type
   * - Device creation
     - dojot.device-manager.device
     - `Creation message`_
   * - Device update
     - dojot.device-manager.device
     - `Update message`_
   * - Device removal
     - dojot.device-manager.device
     - `Removal message`_
   * - Device actuation
     - dojot.device-manager.device
     - `Actuation message`_


Creation message
----------------

This message is published whenever a new device is created.
Its payload is a simple JSON:

.. code-block:: json

    {
      "event": "create",
      "meta": {
        "service": "admin"
      },
      "data": {
        "id": "efac",
        "label" : "Device 1",
        "templates" : [ 1, 2, 3],
        "attrs" : {

        },
        "created" : "2018-02-06T10:43:40.890330+00:00"
      }
    }

And its attributes are:

- *event* (string): "create"
- *meta*: Meta information about the message

  - *service* (string): Tenant associated to this device

- *data*: device data structure

  - *id* (string): Device ID
  - *attrs*: Device attributes. This field is as described in :doc:`./concepts`


Update message
--------------

This message is published whenever a new device is updated.
Its payload looks very similar to device creation:

.. code-block:: json

    {
      "event": "update",
      "meta": {
        "service": "admin"
      },
      "data": {
        "id": "efac",
        "label" : "Device 1",
        "templates" : [ 1, 2, 3],
        "attrs" : {

        },
        "created" : "2018-02-06T10:43:40.890330+00:00"
      }
    }


- *event* (string): "update"
- *meta*: Meta information about the message

  - *service* (string): Tenant associated to this device

- *data*: device new data structure

  - *id* (string): ID of the device being updated
  - *attrs*: Device attributes. This field is as described in :doc:`./concepts`


Removal message
---------------

This message is published whenever a device is removed.
Its payload is:

.. code-block:: json

    {
      "event": "remove",
      "meta": {
        "service": "admin"
      },
      "data": {
        "id": "efac"
      }
    }


- *event* (string): "remove"
- *meta*: Meta information about the message

  - *service* (string): Tenant associated to this device

- *data*: device data

  - *id* (string): ID of the device being removed


Actuation message
-----------------

This message is published whenever a device must be configured.
The payload is:

.. code-block:: json

  {
    "event": "actuate",
    "meta": {
      "service": "admin"
    },
    "data" : {
      "id" : "efac",
      "attrs": {
        "reset" : 1,
        "step-motor" : "+45"
      }
    }
  }


- *event* (string): "actuate"
- *meta*: Meta information about the message

  - *service* (string): Tenant associated to this device

This message should be forwarded to the device. It can contain more attributes
than the ones specified by DeviceManager. For instance, a thermostat could be
configured with the following message:

.. code-block:: json

  {
    "event": "actuate",
    "meta": {
      "service": "admin"
    },
    "data" : {
      "id" : "efac",
      "attrs": {
        "target_temperature" : 23.5
      }
    }
  }

The attribute actually used by the device would be "target_temperature" so that
it can adjust correctly the temperature. It's up to the receiver of this
message (an IoT agent, for instance) to properly send the configuration to the
device.