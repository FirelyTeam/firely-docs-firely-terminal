Remove
======

If you have installed a package in your current project (folder), you
can remove it. For a package called ‘xyz.myprofiles’ the command would
be:

.. code-block:: Bash

   > fhir remove xyz.myprofiles

This will not remove your package from your global packages cache, but
it is no longer part of your current project. The command will update
the manifest (package.json) and your lock file ``fhirpkg.lock.json`` ).
