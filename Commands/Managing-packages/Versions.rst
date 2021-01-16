Versions
========

Lists all available versions of the package on the server

.. code-block:: Bash

   > fhir versions simplifier.core.stu3

This shows you all the available versions of the package on the package
server. What it does is that it fetches the *package listing* of that
package from the server, which is a json document. To see that document,
use the ``--raw`` flag. Example:

.. code-block:: Bash

   > fhir versions simplifier.core.stu3 --raw
