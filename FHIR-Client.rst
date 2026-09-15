FHIR Client 
===========


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
server knows. One example would be to search for a patient name: 

.. code-block:: Bash

   > fhir search https://server.fire.ly Patient name=Chalmers 

It is also possible to retrieve core resources from the admnistration database. 
The example shows how this works for the us-core-patient on the public Firely Server endpoint:

.. code-block:: Bash

   > fhir search http://server.fire.ly/administration StructureDefinition url=http://hl7.org/fhir/us/core/StructureDefinition/us-core-patient



Uploading a package to a FHIR server
------------------------------------

Conformance resources from a FHIR package -- profiles, extensions, ValueSets, CodeSystems --
often need to be loaded onto a FHIR server before you can validate against them there.
There is no single command that uploads a whole package; you combine three commands and
send the resources one by one.

Start by registering the server under an alias and installing the package you want to upload:

.. code-block:: Bash

   > fhir server add myserver https://myserver/fhir
   > fhir install de.basisprofil.r4 1.5.4

``fhir canonicals`` lists the canonical url of every conformance resource in the package,
``fhir resolve`` fetches one of them onto the stack, and ``fhir put`` pops it off the stack
and sends it to the server. Prefer ``fhir put`` over ``fhir post``: it keeps the resource id
that the package author assigned, and it is idempotent, so you can re-run the upload after
updating the package.

.. code-block:: Bash

   > fhir canonicals de.basisprofil.r4 1.5.4 | tr -d '\r' | grep -v '^[[:space:]]*$' | while read -r canonical
     do
       fhir resolve "$canonical"
       fhir put myserver
     done

The same loop in PowerShell:

.. code-block:: PowerShell

   > fhir canonicals de.basisprofil.r4 1.5.4 | Where-Object { $_.Trim() } | ForEach-Object {
         fhir resolve $_
         fhir put myserver
       }

Both loops filter out empty lines, because ``fhir canonicals`` writes a progress indicator
before it writes the list of canonicals.

A few things to be aware of:

* ``fhir canonicals`` only lists the resources of the package you name. Dependencies are separate
  packages: use ``fhir dependencies`` or ``fhir scope`` to see them, and repeat the loop for each
  one. Upload dependencies before the packages that depend on them.
* ``fhir bundle`` produces a bundle of type ``collection`` without ``request`` entries, so it cannot
  be sent to a server as a transaction. Uploading resource by resource is the supported route.
* If the server needs authentication, configure it on the alias before uploading, with
  ``fhir server authorize myserver basic user=<user> password=<password>`` or, for a token,
  ``fhir server header myserver Authorization "Bearer <token>"``.
