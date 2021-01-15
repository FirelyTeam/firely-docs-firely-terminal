Using NPM instead
=================


If you can’t or don’t want to use the fhir command line tool, but you do
need to install packages from Simplifier, you can use *npm*.

Install npm
~~~~~~~~~~~

To get *npm*, you can install nodejs. The npm command line tools is part
of that. You can download nodejs here: https://nodejs.org/en/download/.

Installing a FHIR package with npm
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

To install anything with npm, you need to have a ``package.json`` file.
You can do that with either ``npm create`` or create a ``package.json``
file yourself. It’s sufficient to add two brackets in that file to make
it work: ``{ }``.

To install a FHIR package with all dependencies, this gets you there. In
this example we’ll install package ``hl7.fhir.r4.core``.

.. code-block:: Bash

   npm create
   npm --registry https://packages.simplifier.net install hl7.fhir.r4.core
   npm --registry https://packages.simplifier.net install 

Simplifier as default npm package server
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

If you want to set Simplifier as your default package server, you can do
that with NPM. We recommend you only do that if you don’t need npm for
.js packages.

.. code-block:: Bash

   npm config set registry https://packages.simplifier.net/
   npm install hl7.fhir.r4.core
   npm install 