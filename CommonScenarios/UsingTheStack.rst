How to use the resource stack
=============================

Firely Terminal was build with an internal stack, to allow you to
combine multiple actions on one or more resources. All resource
operations in Firely Terminal are performed on that stack.

We have provided as many as possible atomic operations, so that you can
combine them yourself and give you much more flexibility. This way you
can get multiple resources in one format from a fhir server and then
bundling them before pushing them to a different server.

Using the stack
~~~~~~~~~~~~~~~

The resource stack of Firely Terminal is just like a stack in a your computer.
You can add stuff to the top, and if you perform operations, they do
that with the resource or resources on top of the stack.

You can basically group the types of operations you can do on a stack in
three categories: 

Adding resouces on the stack
----------------------------

There are several ways to get resources on the stack. 
You can select a resource from disk:

.. code-block:: Bash

   > fhir push      Pushes matching files or server resources on the stack

You can get a file from a FHIR server:

.. code-block:: Bash
  
   > fhir read firely.server Patient name=Chalmers

It is also possible to duplicate the top resource on the stack:

.. code-block:: Bash
   
   > fhir duplicate

  
Removing resources from the stack
---------------------------------

You can throw away resources:
 .. code-block:: Bash

    > fhir drop

Clear the whole stack:

.. code-block:: Bash

   > fhir clear


Operations to perform with the stack
------------------------------------

In a classical (number) stack, you would use ``+``, ``-`` or ``x``. With Firely Terminal you get FHIR operations
as shown below:


.. code-block:: Bash

   > fhir save      Saves the file to disk
   > fhir filter    Filters the stack on matching FhirPath expressions
   > fhir reverse   Reverses the order of all resources on the stack
   > fhir sort      Sorts the stack based on the expression
   > fhir swap      Swaps the top 2 elements on the stack
   > fhir show      Displays a resource tree from a file or the top of the stack
   > fhir stack     Lists the items on the stack
   > fhir bundle    Takes all resources on the stack and puts them into a single bundle
   > fhir split     Splits the bundle on the top of the stack into separate resources
   > fhir peek      Displays the top item on the stack

Snapshot Generation
-------------------






