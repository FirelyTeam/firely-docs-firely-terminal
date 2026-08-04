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
     > fhir check --push             #put the resulting issues on the stack as an OperationOutcome

Firely Terminal ships with three rule series:

.. list-table::
   :header-rows: 1
   :widths: 22 20 58

   * - Series
     - Validates?
     - What it does
   * - ``free``
     - **No**
     - Parsing, id existence, version settings, canonical pinning. Needs no license.
   * - ``minimal``
     - Yes
     - The default. Adds validation of all resources against the FHIR standard and their profiles.
   * - ``recommended``
     - Yes
     - ``minimal`` plus snapshot, id, naming and uniqueness rules. What Firely advises for an IG.

Exit codes
----------

``fhir check`` **exits 0 even when it reports errors** — it prints ``FAIL:`` with the issue counts
and still returns success. A non-zero exit code means the check could not run at all: no license, an
unknown series, or not a project folder. Use ``--fail`` when you want error-level findings to return
a non-zero exit code, for example to gate a pipeline:

.. code-block:: Bash

     > fhir check recommended --fail

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
