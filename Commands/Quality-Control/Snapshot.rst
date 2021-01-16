Snapshot
--------

The snapshot command takes a StructureDefinition on top of the stack,
and builds the snapshot element of that StructureDefinition. The
resulting StructureDefinition will be placed back on top of the stack.

Usage
^^^^^

.. code-block:: Bash

   > fhir snapshot

Scope
^^^^^

The command uses the project :ref:`Scope` (current folder) to
resolve all structures needed to build the snapshot.
