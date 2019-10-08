Configuration Types
===================

.. seo::
    :description: Documentation of different configuration types in ESPHome
    :image: settings.png

ESPHome’s configuration files have several configuration types. This
page describes them.

.. _config-id:

ID
--

Quite an important aspect of ESPHome are “ids”. They are used to
connect components from different domains. For example, you define an
output component together with an id and then later specify that same id
in the light component. IDs should always be unique within a
configuration and ESPHome will warn you if you try to use the same
ID twice.

Because ESPHome converts your configuration into C++ code and the
ids are in reality just C++ variable names, they must also adhere to
C++’s naming conventions. `C++ Variable
names <https://venus.cs.qc.cuny.edu/~krishna/cs111/lectures/D3_C++_Variables.pdf>`__
…

-  … must start with a letter and can end with numbers.
-  … must not have a space in the name.
-  … can not have special characters except the underscore (“_“).
-  … must not be a keyword.

.. _config-pin:

Pin
---

ESPHome always uses the **chip-internal GPIO numbers**. These
internal numbers are always integers like ``16`` and can be prefixed by
``GPIO``. For example to use the pin with the **internal** GPIO number 16,
you could type ``GPIO16`` or just ``16``.

Most boards however have aliases for certain pins. For example the NodeMCU
ESP8266 uses pin names ``D0`` through ``D8`` as aliases for the internal GPIO
pin numbers. Each board (defined in :doc:`ESPHome section </components/esphome>`)
has their own aliases and so not all of them are supported yet. For example,
for the ``D0`` (as printed on the PCB silkscreen) pin on the NodeMCU ESP8266
has the internal GPIO name ``GPIO16``, but also has an alias ``D0``. So using
either one of these names in your configuration will lead to the same result.

.. code-block:: yaml

    some_config_option:
      pin: GPIO16

    some_config_option:
      # alias on the NodeMCU ESP8266:
      pin: D0

.. _config-pin_schema:

Pin Schema
----------

In some places, ESPHome also supports a more advanced “pin schema”.

.. code-block:: yaml

    some_config_option:
      # Basic:
      pin: D0

      # Advanced:
      pin:
        number: D0
        inverted: True
        mode: INPUT_PULLUP

Configuration variables:

-  **number** (**Required**, pin): The pin number.
-  **inverted** (*Optional*, boolean): If all read and written values
   should be treated as inverted. Defaults to ``False``.
-  **mode** (*Optional*, string): A pin mode to set for the pin at
   startup, corresponds to Arduino’s ``pinMode`` call.

Available Pin Modes:

-  ``INPUT``
-  ``OUTPUT``
-  ``OUTPUT_OPEN_DRAIN``
-  ``OPEN_DRAIN``
-  ``ANALOG`` (only on ESP32)
-  ``INPUT_PULLUP``
-  ``INPUT_PULLDOWN`` (only on ESP32)
-  ``INPUT_PULLDOWN_16`` (only on ESP8266 and only on GPIO16)

More exotic Pin Modes are also supported, but rarely used:

-  ``WAKEUP_PULLUP`` (only on ESP8266)
-  ``WAKEUP_PULLDOWN`` (only on ESP8266)
-  ``SPECIAL``
-  ``FUNCTION_0`` (only on ESP8266)
-  ``FUNCTION_1``
-  ``FUNCTION_2``
-  ``FUNCTION_3``
-  ``FUNCTION_4``
-  ``FUNCTION_5`` (only on ESP32)
-  ``FUNCTION_6`` (only on ESP32)

.. _config-time:

Time
----

In lots of places in ESPHome you need to define time periods.
There are several ways of doing this. See below examples to see how you can specify time periods:

.. code-block:: yaml

    some_config_option:
      some_time_option: 1000us  # 1000 microseconds = 1ms
      some_time_option: 1000ms  # 1000 milliseconds
      some_time_option: 1.5s  # 1.5 seconds
      some_time_option: 0.5min  # half a minute
      some_time_option: 2h  # 2 hours

      # Make sure you wrap these in quotes
      some_time_option: '2:01'  # 2 hours 1 minute
      some_time_option: '2:01:30'  # 2 hours 1 minute 30 seconds

      # 10ms + 30s + 25min + 3h
      some_time_option:
        milliseconds: 10
        seconds: 30
        minutes: 25
        hours: 3
        days: 0

      # for all 'update_interval' options, also
      update_interval: never  # never update
      update_interval: 0ms  # update in every loop() iteration

.. _config-substitutions:

Substitutions
-------------

Starting with version 1.10.0, ESPHome has a powerful new way to reduce repetition in configuration files:
Substitutions. With substitutions, you can have a single generic source file for all nodes of one kind and
substitute expressions in.

.. code-block:: yaml

    substitutions:
      devicename: livingroom
      upper_devicename: Livingroom

    esphome:
      name: $devicename
      # ...

    sensor:
    - platform: dht
      # ...
      temperature:
        name: ${upper_devicename} Temperature
      humidity:
        name: ${upper_devicename} Humidity

In the top-level ``substitutions`` section, you can put as many key-value pairs as you want. Before
validating your configuration, ESPHome will automatically replace all occurrences of substitutions
by their value. The syntax for a substitution is based on bash and is case-sensitive: ``$substitution_key`` or
``${substitution_key}`` (same).

Additionally, you can use the YAML ``<<`` syntax to create a single YAML file from which a number
of nodes inherit:

.. code-block:: yaml

    # In common.yaml
    esphome:
      name: $devicename
      # ...

    sensor:
    - platform: dht
      # ...
      temperature:
        name: ${upper_devicename} Temperature
      humidity:
        name: ${upper_devicename} Humidity

.. code-block:: yaml

    # In nodemcu1.yaml
    substitutions:
      devicename: nodemcu1
      upper_devicename: NodeMCU 1

    <<: !include common.yaml

.. tip::

    To hide these base files from the dashboard, you can

    - Place them in a subdirectory (dashboard only shows files in top-level dir)
    - Prepend a dot to the filename, like ``.base.yaml``

See Also
--------

- :doc:`ESPHome index </index>`
- :doc:`getting_started_command_line`
- :doc:`faq`
- :ghedit:`Edit`
