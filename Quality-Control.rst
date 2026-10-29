.. _quality_control:

Quality Control
================

Quality Control checks your project against a set of rules that catch the mistakes a plain
validation run does not: missing metadata, inconsistent canonicals, naming and documentation
conventions, unused elements, and so on. It is available in Firely Terminal from v2.1 with the
``fhir check`` command.

.. note::
   Quality Control is a **licensed feature**, included in the Simplifier.net Professional plan and
   higher. Log in with an account that has such a license before running the checks:

   .. code-block:: Bash

        > fhir login email=<your email> password=<your password>
        > fhir license      #shows which licensed features are available to you

   The ``free`` rule series is the exception: it runs without a license, but it does not validate
   your resources.

Running the checks
------------------

Run ``fhir check`` from your project folder. Without arguments it runs the ``minimal`` rule set;
add a rule series name to run a different one:

.. code-block:: Bash

     > fhir check                    #runs the minimal rule set
     > fhir check recommended        #runs the recommended rule set
     > fhir check --list --details   #list the rules in the set without executing them
     > fhir check --push             #put the resulting issues on the stack as a Bundle
     > fhir check --output qc.json   #save the resulting issues to a file as a Bundle

Firely Terminal ships with the same rule series as Simplifier's default scripts:

.. list-table::
   :header-rows: 1
   :widths: 22 20 58

   * - Series
     - Validates?
     - What it does
   * - ``free``
     - **No**
     - Parsing, a mandatory id on every resource, and the project's canonical claims. Needs no license.
   * - ``minimal``
     - Yes
     - The default. Parsing, canonical claims, validation of all resources against the FHIR standard
       and their profiles, unique StructureDefinition canonicals, and the ``manifest`` series.
   * - ``manifest``
     - No
     - Checks ``package.json``: that there is exactly one, that its contents are valid, and its
       dependencies.
   * - ``recommended``
     - Yes
     - ``minimal`` plus no pre-generated snapshots, valid ids, StructureDefinition names without
       spaces, and unique names. What Firely advises for an IG.

.. note::
   These series changed in Firely Terminal 4.0.0 to match Simplifier. ``minimal`` now includes
   ``manifest``, so a ``package.json`` that misses a required field, such as ``author``, fails
   ``minimal``. The mandatory id check moved from ``recommended`` to ``free``. Run
   ``fhir check --list --details`` to see the rules in a series.

Exit codes
----------

``fhir check`` **exits 0 even when it reports errors** — it prints ``FAIL:`` with the issue counts
and still returns success. A non-zero exit code means the check could not run at all: no license, an
unknown series, or not a project folder. Use ``--fail-on`` to choose the severity that makes it exit
with code 1, for example to gate a pipeline:

.. code-block:: Bash

     > fhir check recommended --fail-on error     #exit 1 on errors
     > fhir check recommended --fail-on warning   #exit 1 on warnings and errors

The older ``--fail`` flag still works but is deprecated. On ``fhir check`` it exits with code 1 on
any issue, including warnings and information. It cannot be combined with ``--fail-on``.

Results as FHIR
---------------

``--output`` and ``--push`` give the results as a ``Bundle`` of type ``collection`` with one
``OperationOutcome`` per file, in the shape the HL7 Java validator uses, so tools that read one can
read the other:

* The file is named in the ``http://hl7.org/fhir/StructureDefinition/operationoutcome-file``
  extension on each OperationOutcome. Issues that belong to the whole project, such as a missing
  canonical claim, have their own OperationOutcome without it.
* The message of each issue is in ``issue.details.text``, and the rule code in
  ``issue.details.coding``.

The extension of the ``--output`` file decides the format: ``.json`` or ``.xml``. Save it outside
your project folder, or the next ``fhir check`` reads it as part of your project.

.. note::
   Before Firely Terminal 4.0.0, ``--push`` gave a single OperationOutcome, with the message in
   ``issue.diagnostics`` and no file names. Tooling that reads it needs updating.

Custom rule sets
----------------

You can write your own rules. A rule set lives in a file named ``<series>.rules.yaml``, for example
``mychecks.rules.yaml``, and you run it by passing the series name: ``fhir check mychecks``.

Note that a series is a **name, not a path**: Firely Terminal looks for ``<series>.rules.yaml`` in
your project folder.

Further reading
---------------

For the full list of rules, their meaning, and how to author your own rule sets, see the
`Quality Control documentation <https://docs.fire.ly/projects/Simplifier/quality_control/quality_control.html>`_
on the Simplifier.net documentation site, including the
:ref:`minimal ruleset <simplifier_docs:qc>`.

To run these checks on every push or pull request and fail the build on errors, see
:ref:`automating_quality_control`.
