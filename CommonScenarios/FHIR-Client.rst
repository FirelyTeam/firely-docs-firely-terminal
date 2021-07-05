FHIR Client
~~~~~~~~~~~

Firely Terminal can be used as a FHIR Client similar to Postman. You can create, 
read or delete any specific resource you have access to. 
In these examples, I will use Firely Server as the server to communicate with,
but you can add and use any other server, like this:

.. code-block:: Bash

   > fhir server add <alias> https://myserver/fhir

Search The basic search command requires a fhir server (alias), a
resource type. This example searches for any patient named Chalmers on the public Firely Server.
As a default the public endpoint http://server.fire.ly is available under the alias ``firely``. 
You can use the public endpoint to retrieve any patient available on the server:

.. code-block:: Bash

   > fhir search firely Patient

The resulting resources will be pushed onto the stack. After that you
can perform any of the other operations that Firely Terminal provides,
like saving them to your working directory:

.. code-block:: Bash

   > fhir save --all

You can also create a tree rendering as you are used to on Simplifier or in Forge of 
the specific resource on the stack.

.. code-block:: Bash

   > fhir show



Count
-----

The default result set maximum is 10 resources, but you can change that
with the same parameter that you would use in a FHIR search URL:

.. code-block:: Bash

   > fhir search firely Patient _count=20

Search parameters
-----------------

With Firely Terminal you can use all search parameters that a FHIR
server knows.

.. code-block:: Bash

   > fhir search https://server.fire.ly Patient name=Chalmers 

It is also possible to retrieve core resources from the admnistration database. 
The example shows how this works for the us-core-patient on the public Firely Server endpoint.

.. code-block:: Bash

   > fhir search http://server.fire.ly/administration StructureDefinition url=http://hl7.org/fhir/us/core/StructureDefinition/us-core-patient
