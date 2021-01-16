Semver
======

The semver command allows you to test if a version range is what you
intended. It will show you the version of a package the range actually
resolves to. If the package ``xyz.myprofiles`` has three versions:
3.1.0, 3.2.0 and 4.0.0, the range ``3.x`` points to ``3.2.0`` Usage:

.. code-block:: Bash

   > fhir semver xyz.myprofiles 3.x
