XML to JSON and vice versa
==========================

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
