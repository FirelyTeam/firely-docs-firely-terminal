Resource Operations
===================

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

   > fhir push      #Pushes matching files or server resources on the stack

You can get a file from a FHIR server:

.. code-block:: Bash
  
   > fhir read firely Patient name=Chalmers

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
-------------------------------------

In a classical (number) stack, you would use ``+``, ``-`` or ``x``. With Firely Terminal you get FHIR operations
as shown below:


.. code-block:: Bash

   > fhir save      #Saves the file to disk
   > fhir filter    #Filters the stack on matching FhirPath expressions
   > fhir reverse   #Reverses the order of all resources on the stack
   > fhir sort      #Sorts the stack based on the expression
   > fhir swap      #Swaps the top 2 elements on the stack
   > fhir show      #Displays a resource tree from a file or the top of the stack
   > fhir stack     #Lists the items on the stack
   > fhir bundle    #Takes all resources on the stack and puts them into a single bundle
   > fhir split     #Splits the bundle on the top of the stack into separate resources
   > fhir peek      #Displays the top item on the stack

Snapshot Generation
~~~~~~~~~~~~~~~~~~~~
To generate a snapshot for a StructureDefinition, Firely Terminal needs to have
two things in place:

1. A StructureDefinition has to be on the stack. 
2. The :ref:`scope <scope>` needs to be set. 

Get a StructureDefinition
-------------------------

Put a StructureDefinition on the stack. You can do this by fetching a structure
definition from a server or from disk;

.. code-block:: Bash

     > fhir read firely StructureDefinition/Patient

Create a scope
--------------

You also need a *scope* for resolving resources and data types. You can
create a scope in any directory, by installing the required packages.
For example:

.. code-block:: Bash

     > fhir install hl7.fhir.r3.core

Generate the snapshot
-----------------------

After this you can update the snapshot in the StructureDefinition, by
one simple statement:

.. code-block:: Bash

     > fhir snapshot

After this the resource on the stack is replaced with a new
StructureDefinition that contains the just generated snapshot. Use ``fhir save``
to write it back to disk.

.. _snapshotting_a_project:

Snapshotting a whole project or package
---------------------------------------

``fhir snapshot`` works on one resource at a time. To generate snapshots for *all* profiles
at once - and expand all ValueSets along with them - use ``fhir inflate``. It always needs
to be told *what* to inflate:

.. code-block:: Bash

   > fhir inflate --here                     #the current project (licensed)
   > fhir inflate --package xyz.myprofiles   #a package in your cache
   > fhir inflate --dependencies             #the packages your project depends on
   > fhir deflate                            #removes the snapshots and expansions again

.. note::
   Inflating the current project (``--here``) requires a license, as does ``fhir deflate``
   on it. Inflating a package (``--package``) or your dependencies does not.

Add ``--snapshot`` or ``--expand`` to limit it to one of the two:

.. code-block:: Bash

   > fhir inflate --here --snapshot          #snapshots only, no ValueSet expansions

Like ``fhir snapshot``, inflating resolves against the current :ref:`scope <scope>`, so
install your dependencies first.

Firely Terminal records that a folder has been inflated (in ``.inflator.json``), so running
``fhir inflate`` on it a second time does nothing. Use ``--force`` to regenerate anyway, for
example after changing a differential.

.. warning::
   ``fhir inflate`` rewrites the files it touches: the generated snapshots and expansions
   become part of your profiles and ValueSets permanently. All Firely tools generate
   snapshots on the fly, so you only need this when handing your resources to tooling that
   cannot. Use ``fhir deflate`` to undo it.

To get snapshots into a package at *packaging* time instead, use a ``package.bake.yaml``
script - see :ref:`Managing packages <managing_packages>`.


Validation
~~~~~~~~~~

The validate command validates the resource on the top of the stack,
using the current :ref:`scope`.


.. _scope:

Scope
-----

Validation needs a lot of data: from StructureDefinitions on the
resource itself, to data types, Extensions and ValueSets. It will look
into your project (current folder) and any packages, whether dependencies
or dependencies of dependencies (etc.) to find all these assets.

Output
------

Validation will report any errors and warnings and information messages
to the command line output.

.. code-block:: Bash

   > fhir validate

Parameters
----------

You can specify an additional profile to validate against, and choose which
validator engine to use. For more details on these parameters, see:

.. code-block:: Bash

   > fhir ? validate

Validating a whole project
--------------------------

``fhir validate`` works on one resource at a time. To validate every resource in your project at
once - which is what you want in a pipeline - use ``fhir check`` instead. See
:ref:`automating_quality_control`.


XML to JSON and vice versa
~~~~~~~~~~~~~~~~~~~~~~~~~~

With Firely Terminal you can save a FHIR resource either as JSON or XML. This
also allows you to convert from one format to the other. Here are some
examples.

In order to allow you to perform multiple operations on a resource,
Firely Terminal works with an internal stack where you push your resources to.
In case of converting to another format, you just have to save those
resources from the stack to the preferred format.

With this command you save the resource on the top of the stack as an
XML file:

.. code-block:: Bash

   > fhir save --xml

Converting a file to JSON
-------------------------

Load a resource from disk:

.. code-block:: Bash

   > fhir push mypatient.xml

And save it as json:

.. code-block:: Bash

   > fhir save mypatient.json

Firely Terminal will automatically recognize from the target file extension that
you want to transform the resource JSON.

Converting a file to XML
------------------------

Of course the other way around works the same:

.. code-block:: Bash

   > fhir push mylabreport.json
   > fhir save mylabreport.xml

Converting all files in a folder:
---------------------------------

.. code-block:: Bash

   > fhir push *.xml
   > fhir save --all --json

Getting JSON files out a of an XML server:
------------------------------------------

In the example here, use your own alias and of course a different URL:

.. code-block:: Bash

   > fhir server add myserver https://myxmlfhirserver.com/fhir/
   > fhir search myserver Patient _count=20
   > fhir save --all --json

