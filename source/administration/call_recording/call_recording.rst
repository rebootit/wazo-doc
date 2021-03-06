.. _call_recording:

**************
Call Recording
**************

Call recording allow the user of the administrator to record a user's conversation. Recorded files
are stored on the Wazo server and are accessible using the web interface.


Enabling
========

There are many ways to enable call recording. It can be done by the administrator or the user himself.


Administrator
-------------

The administrator can enable call recording from the user form in the web interface.

.. figure:: images/webi_call_recording.png
   :scale: 85%


User
----

The user can enable and disable call recording using the `*26` extension on its phone.
The user can also enable call recording during a call using the `*3` extension during the conversation.


Call Recording Management
=========================

Extensions
----------

The extensions for call recording and online call recording are available in the web interface
in the extension form.

.. figure:: images/webi_call_recording_extensions.png
   :scale: 85%


Disable user call control management
------------------------------------

To disable call recording for user, disable the `Call recording` extension in the web interface.

To disable online call recording, uncheck the `Enable online call recording` option in the user form.

.. figure:: images/webi_online_call_recording.png
   :scale: 85%


Files
-----

Recorded files are available in the web interface in the `Audio files`, `monitor` menu.

.. figure:: images/webi_call_recording_files.png
   :scale: 85%

Recordings are located in `/var/spool/asterisk/monitor`


File names
^^^^^^^^^^

The file names for call recording can be customized using `Jinja2 <http://jinja.pocoo.org/docs/2.9/templates/>`_ templates.

The following variables can be used in the file name:

* srcnum: The caller ID number of the caller
* dstnum: The called extension
* timestamp: A unix timestamp
* local_time: The formated date in the server's timezone
* utc_time: The formated date in UTC
* base_context: The context in which this call entered the Wazo dialplan
* tenant_uuid: The tenant UUID of the user or the outgoing call

.. note::
  You **must** restart xivo-agid to take any config change into effect::

     systemctl restart xivo-agid

Example 1:

Creating recording in a sub-directory for each entity

A file with the following content in `/etc/xivo-agid/conf.d/call_recording.yml`:

.. code-block:: yaml

   call_recording:
     filename_template: "{{ tenant_uuid }}/{{ utc_time }}-{{ srcnum }}-{{ dstnum }}"


This configuration would write the files in `/var/spool/asterisk/monitor/<tenant_uuid>/`. The
name of the files would be `<utc_time>-<srcnum>-<dstnum>.wav`

Example 2:

Creating recording in another directory

A file with the following content in `/etc/xivo-agid/conf.d/call_recording.yml`:

.. code-block:: yaml

   call_recording:
     filename_template: "/home/pcm/call/user-{{ srcnum }}-{{ dstnum }}-{{ timestamp }}"

This configuration would write the files in the `/home/pcm/call` directory. The name of the
files would be `user-<srcnum>-<dstnum>-<timestamp>.wav`. Which is the default with another
location.

.. note:: recording that are not directly in `/var/spool/asterisk/monitor` will not be shown
   in the web interface.

.. note:: Asterisk needs write permission to be able to write the recordings in the configured
   directory.

The filename for online call recording cannot be configured from the configuration file but
can be modified using a pre-process subroutine.

The file format is always `auto-timestamp-<TOUCH_MIXMONITOR>.wav`. TOUCH_MIXMONITOR is a
channel variable that can be set before the call starts.


File extensions
^^^^^^^^^^^^^^^

For online call recording, the file format can be modified using the `TOUCH_MIXMONITOR_FORMAT`
channel variable.

For call recording the default value is `wav` and can be modified with a configuration file.

Example:

Add a file names `/etc/xivo-agid/conf.d/recording.yml` with the following content:

.. code-block:: yaml

   call-recording:
     filename_extension: wav
