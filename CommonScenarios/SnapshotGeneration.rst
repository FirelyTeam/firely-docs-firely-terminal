Snapshot Generation
===================

To generate a snapshot for a StructureDefinition, Firely Terminal needs to have
two thing in place:

Get a StructureDefinition
~~~~~~~~~~~~~~~~~~~~~~~~~~~~

A snapshot on the stack. You can do this by fetching a structure
definition from a server or from disk;

.. code-block:: Bash

     > fhir read Vonk StructureDefinition/Patient

Create a scope
~~~~~~~~~~~~~~~~~

You also need a *scope* for resolving resources and data types. You can
create a scope in any directory, by installing the required packages.
For example:

.. code-block:: Bash

     > fhir install hl7.fhir.r3.core

Generate the snapshot
~~~~~~~~~~~~~~~~~~~~~

After this you can update the snapshot in the StructureDefinition, by
one simple statement:

.. code-block:: Bash

     > fhir snapshot

After this the resource on the stack is replaced with a new
StructureDefinition that contains the just generated snapshot.
