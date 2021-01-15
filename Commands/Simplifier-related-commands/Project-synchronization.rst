Project Synchronization
-----------------------

This documentation is for Firely Terminal version 0.9.6 or higher.

You can keep a local folder in sync with a project in Simplifier by
using the ``sync`` command. For that it uses the zip api endpoint of
Simplifier.

In order to sync with a project in Simplifier, you have to be logged in.
Read more on this here: {{link:loggingin}}.

Synchronization has several options.

Download
~~~~~~~~

To download the latest content of a Simplifier project to a local
folder, use the ``--down`` flag:

.. code-block:: Bash

       > fhir sync --down

This will update all files in your current folder that are newer or
missing.

Upload
~~~~~~

To upload the contents of a local folder to a Simplifier use the
``--up`` command.

.. code-block:: Bash

       > fhir sync --up

Bidirectional
~~~~~~~~~~~~~

You can synchronize both ways using a combination of the ``--up`` and
``--down`` flag:

.. code-block:: Bash

       > fhir sync --up --down

or, using short flags:

.. code-block:: Bash

       > fhir sync -ud

Subfolders
~~~~~~~~~~

If you want to include all subfolders of the current folder in
synchronization, use the ``--folders`` flag. This works both for
uploading and for downloading. For Files that are in folders that don’t
exist in the target location, the subfolders will be created.

Details
~~~~~~~

In order to get more detailed information during the sync process, use
the flag ``--verbose``. This will show each individual file that is
being synchronized. This works both for syncing up and for syncing down.

Synchronizing deletes
~~~~~~~~~~~~~~~~~~~~~

It is possible to synchronize deletes when doing a down sync. With an up
sync this is not yet possible. You can enable this by using the
``--clean`` flag.

**Warning:** this will delete all files in your folder that are not
present in the project in Simplifier!

To make sure that files are not deleted by accident, you can only run a
*clean* sync by explicitly providing the target folder from the root.
For example:

.. code-block:: Bash

       > fhir sync myproject c:/fhirprojects/myproject --down --clean