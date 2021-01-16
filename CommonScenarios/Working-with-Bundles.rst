Working with Bundles
====================

With Firely Terminal you can bundle a set of resources or tear a bundle apart.

Bundle all resources in a folder
--------------------------------

In this example, we assume, you have a set of xml resources. The first
step is to load all the resources in the folder onto the stack.

.. code-block:: Bash

   > fhir push *.xml

After that the following command takes all resources on the stack, put
them in a single bundle and set that Bundle resource on the stack.

.. code-block:: Bash

   > fhir bundle

The save commmand can then be used to get your bundle on disk

.. code-block:: Bash

   > fhir save mybundle

Splitting a bundle
------------------

The inverse scenario can be done with the same example: Load a bundle
onto the stack; use the Split command and save the results:

.. code-block:: Bash

   > fhir push mybundle.xml
   > fhir split
   > fhir save --all
