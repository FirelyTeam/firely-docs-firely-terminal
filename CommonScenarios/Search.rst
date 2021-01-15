Search
~~~~~~

In these examples, I will use vonk as the server to communicate with,
but you can add and use any other server, like this:

.. code-block:: Bash

   > fhir server add myalias https://myserver/fhir

Search The basic search command requires a fhir server (alias), a
resource type. This exmaple searches for any patient on the Vonk server.

.. code-block:: Bash

   > fhir search vonk Patient

The resulting resources will be pushed onto the stack. After that you
can perform any of the other operations that Firely Terminal provides,
like saving them to disk:

.. code-block:: Bash

   > fhir save --all

Count
-----

The default result set maximum is 10 resources, but you can change that
with the same parameter that you would use in a FHIR search URL:

.. code-block:: Bash

   > fhir search vonk Patient _count=20

Search parameters
-----------------

With Firely Terminal you can use all search parameters that a FHIR
server knows.

.. code-block:: Bash

   > fhir search vonk Patient name=Chalmers 

Getting the core Patient structure definition:

.. code-block:: Bash

   > fhir search vonk StructureDefinition url=http://hl7.org/fhir/StructureDefinition/Patient
