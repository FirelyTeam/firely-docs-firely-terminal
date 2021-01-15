Simplifier projects as a FHIR server
------------------------------------

Firely Terminal understands that projects in Simplifier are a fhir
server. You dont have to configure anything to make use of them. To get
a fhir resource (in this example, the example patient) from the FHIR
endpoint of your project, you can simply use:

.. code-block:: Bash

   > fhir read myproject Patient/example
