Install
=======

The install command can be used to install any FHIR package from the
package server. It will install the package on your local machine and
add a reference from your current folder to that package, by adding it
to the ``package.json`` file (also known as a package manifest). You can
provide just the package name, to get the latest.

.. code-block:: Bash

   > fhir install xyz.myprofiles 

Or you can specify a specific version or range.

.. code-block:: Bash

   > fhir install xyz.myprofiles 2.3

Folder install
--------------

By default a package is installed in the global package cache of your
machine. But you can specify to install it in the current folder with
the ``--here`` (``-r``) flag.

.. code-block:: Bash

   > fhir install simplifier.core.stu3 --here

This will install the latest version of package \`simplifier.core.stu3’
as a subfolder of your current folder. In most scenario’s you should not
need this option.

Install a file
--------------

If you have a package file that is not on the package server, for
example you created it locally and want to test it, you cana install a
package file (.tgz) from your current folder into the global package
cache of your machine. For this you can use the ``--file`` (``-f``)
flag.

.. code-block:: Bash

   > fhir install thisproject.tgz --file
