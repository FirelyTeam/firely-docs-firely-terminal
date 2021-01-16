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

1. Adding resources to the stack
---------------------------------

With commands like pusing a file from disk on top of the stack

.. code-block:: Bash

   > fhir push mypatient.xml

Or getting a file from a server:

.. code-block:: Bash

   > fhir read vonk Patient name=Chalmers

Duplicating the top resource on the stack:

.. code-block:: Bash

   > fhir dup

2. Taking resources from the stack:
-----------------------------------

Just throwing a resource away:

.. code-block:: Bash

   > fhir drop

Clearing the whole stack:

.. code-block:: Bash

   > fhir clear

But also saving files:

.. code-block:: Bash

   > fhir save mypatient.xml

3. Operations
-------------

In a classical (number) stack, you would use plus, minues or times. With
Firely Terminal you get FHir operations like switching the top two resources:

.. code-block:: Bash

   > fhir swap

Bundling all resources

.. code-block:: Bash

   > fhir bundle

This will remove all resources from the stack and putting them back as a
single FHIR Bundle resource. Another is to just show what is there:

.. code-block:: Bash

   > fhir show