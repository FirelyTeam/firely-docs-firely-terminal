Post
====

You can post a resource to a FHIR server, using the ``post`` command:

.. code-block:: Bash

   > fhir post <server>

What this does is that it takes the resource from the top of the stack
and posts that using the FHIR protocol to FHIR server you choose. Below
is a full scenario, to mak that clear:

Scenario
--------

Let’s add a server alias to the known server list of Firely Terminal. This can
be any FHIR server.

.. code-block:: Bash

   > fhir server add vonk https://vonk.fire.ly

We can now use a server called ‘vonk’. Next, let’s get a resource from
that server: a patient, with id ``example``:

.. code-block:: Bash

   > fhir read vonk Patient/example

The patient record has been fetched from the server and pushed on the
local stack.

Now we can post this resource to another server, and let’s take a
project on Simplifier that you own, because every STU3 FHIR project in
Simplifier has it’s own FHIR server. Let’s imagine it to be ‘myproject’.

.. code-block:: Bash

   > fhir post myproject

The resource is removed from the stack and posted as a new resource to
that project.
