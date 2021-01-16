Validate
--------

The validate command validates the resource on the top of the stack,
using the current :ref:`Scope`.

Scope
~~~~~

Validation needs a lot of data: from StructureDefinitions on the
resource itself, to data types, Extensions and ValueSets. It will look
into your project (current folder) and any packages, wether dependencies
or depenencies of dependencies (etc.) to find all these assets.

Output
~~~~~~

Validation will report any errors and warnings and information messages
to the command line output.

.. code-block:: Bash

   > fhir validate

Parameters
~~~~~~~~~~

You can use your own terminology server or specifiy an additional
profile to validate against. For more details on these paremters, see:

.. code-block:: Bash

   > fhir ? validate
