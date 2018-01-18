DeviceManager concepts
======================

Here are the main concepts needed to correctly use DeviceManager. They are not
hard to understand, but they are essential to operate not only DeviceManager,
but the dojot platform as well.

Device
------

In dojot, a device is a digital representation of an actual device or gateway
with one or more sensors or of a virtual one with sensors/attributes inferred
from other devices.

Consider, for instance, an actual device with temperature and humidity sensors;
it can be represented into dojot as a device with two attributes (one for each
sensor). We call this kind of device as regular device or by its communication
protocol, for instance, MQTT device or CoAP device.

We can also create devices which don’t directly correspond to their associated
physical ones, for instance, we can create one with higher level of information
of temperature (is becoming hotter or is becoming colder) whose values are
inferred from temperature sensors of other devices. This kind of device is
called virtual device.

The information model used for both "real" and virtual devices is as
following:

.. list-table:: Device structure
   :header-rows: 1
   :stub-columns: 1

   * - Attribute
     - Type and mode
     - Description
   * - id
     - String (read only)
     - This is the identifier that will be used when referring to 
       this device.
   * - label
     - String (read-write, required)
     - An user label to identify this device more easily
   * - created
     - DateTime (read-only)
     - Device creation date
   * - updated
     - DateTime (read-only)
     - Device update date
   * - templates
     - [ String (template ID) ] (read-write)
     - List of template IDs to “assemble” this device (more on this on 
       ‘Template’ section)
   * - attrs
     - [ Attributes ] (read-only)
     - Map of attributes currently set to this device.

The *attrs* attribute is, in fact, a map associating a template ID with an
attribute.

.. list-table:: Attribute structure
   :header-rows: 1
   :stub-columns: 1

   * - Attribute
     - Type and mode
     - Description
   * - id
     - integer (read-write) 
     - Attribute ID (automatically generated)
   * - label
     - string (read-write, required) 
     - User label for this attribute
   * - created
     - DateTime (read-only) 
     - Attribute creation date
   * - updated
     - DateTime (read-only) 
     - Attribute update date
   * - type
     - string (read-write, required) 
     - Attribute type (“static”, “dynamic”, "actuator")
   * - value_type
     - string (read-write, required) 
     - Attribute value type (“string”, “float”, “integer”, “geo”)
   * - static_value
     - string (read-write) 
     - If this is a static attribute, which is its static value
   * - template_id
     - string (read-write) 
     - From which template did this attribute come from.

All attributes that are read/write can be used when creating or updating the device.
All of them are returned (if that makes sense - for instance, static_value won't
be returned when no value is set to it) when retrieving device data.

An example of such structure would be:

.. code-block:: json

  {
    "templates": [
      1,
      2
    ],
    "created": "2018-01-05T17:33:31.605748+00:00",
    "attrs": {
      "1": [
        {
          "template_id": "1",
          "created": "2018-01-05T15:41:54.840116+00:00",
          "label": "temperature",
          "value_type": "float",
          "type": "dynamic",
          "id": 1
        }
        {
          "static_value": "SuperTemplate Rev01",
          "created": "2018-01-05T15:41:54.883507+00:00",
          "label": "model",
          "value_type": "string",
          "type": "static",
          "id": 3,
          "template_id": "1"
        }
      ],
      "2": [
        {
          "static_value": "/admin/efac/attrs",
          "template_id": "2",
          "created": "2018-01-05T15:47:02.995541+00:00",
          "label": "mqtt-topic",
          "value_type": "string",
          "type": "meta",
          "id": 4
        }
      ]
    },
    "id": "b7bd",
    "label": "device"
  }

Template
--------

All devices are created based on a *template*, which can be thought as a
model of a device. As “model” we could think of part numbers or product
models - one *prototype* from which devices are created. Templates in
dojot have one label (any alphanumeric sequence), a list of attributes
which will hold all the device emitted information, and optionally a few
special attributes which will indicate how the device communicates,
including transmission methods (protocol, ports, etc.) and message
formats.

In fact, templates can represent not only “device models”, but it can
also abstract a “class of devices”. For instance, we could have one
template to represent all themometers that will be used in dojot. This
template would have only one attribute called, let’s say, “temperature”.
While creating the device, the user would select its “physical
template”, let’s say *TexasInstr882*, and the ‘thermometer’ template.
The user would have also to add translation instructions in order to map
the temperature reading that will be sent from the device to a
“temperature” attribute.

In order to create a device, a user selects which templates are going to
compose this new device. All their attributes are merged together and
associated to it - they are tightly linked to the original template so
that any template update will reflect all associated devices.

The information model used for templates is:

.. list-table:: Template structure
   :header-rows: 1
   :stub-columns: 1

   * - Attribute
     - Type and mode
     - Description
   * - *id*
     - string (read-write)
     - This is the identifier that will be used when referring to this template
   * - *label*
     - string (read-write, required)
     - An user label to identify this template more easily
   * - *created*
     - DateTime (read-only)
     - Template creation date
   * - *updated*
     - DateTime (read-only)
     - Template update date
   * - *attrs*
     - [ Attributes ] (read-write)
     - List of attributes currently set to this template - it’s the same as *attributes* from Device section.

An example of such structure would be:

.. code-block:: json

  {
    "created": "2018-01-05T15:41:54.803052+00:00",
    "attrs": [
      {
        "template_id": "1",
        "created": "2018-01-05T15:41:54.840116+00:00",
        "label": "temperature",
        "value_type": "float",
        "type": "dynamic",
        "id": 1
      },
      {
        "template_id": "1",
        "created": "2018-01-05T15:41:54.882169+00:00",
        "label": "pressure",
        "value_type": "float",
        "type": "dynamic",
        "id": 2
      },
      {
        "static_value": "SuperTemplate Rev01",
        "created": "2018-01-05T15:41:54.883507+00:00",
        "label": "model",
        "value_type": "string",
        "type": "static",
        "id": 3,
        "template_id": "1"
      }
    ],
    "id": 1,
    "label": "Sample Template"
  }


All attributes that are read/write can be used when creating or updating the template.
All of them are returned (if that makes sense - for instance, static_value won't
be returned when no value is set to it) when retrieving device data.