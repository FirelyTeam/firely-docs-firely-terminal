.. _quality_control:

Quality Control
================

Quality Control checks your project against a set of rules that catch the mistakes a plain
validation run does not: missing metadata, inconsistent canonicals, naming and documentation
conventions, unused elements, and so on. It is available in Firely Terminal from v2.1 with the
``fhir check`` command.

.. note::
   Quality Control is a **licensed feature**, included in the Simplifier.net Professional plan and
   higher. You need to be logged in with an account that has such a license, otherwise
   ``fhir check`` will refuse to run:

   .. code-block:: Bash

        > fhir login email=<your email> password=<your password>
        > fhir license      #shows which licensed features are available to you

Running the checks
------------------

Run ``fhir check`` from your project folder. Without arguments it runs the ``minimal`` rule set;
add a rule series name to run a different one. The ``recommended`` set is the one Firely advises for
Implementation Guides and ships with Firely Terminal as well:

.. code-block:: Bash

     > fhir check                    #runs the minimal rule set
     > fhir check recommended        #runs the recommended rule set
     > fhir check --list --details   #list the rules in the set without executing them
     > fhir check --push             #put the resulting issues on the stack as OperationOutcomes

Custom rule sets
----------------

You can write your own rules. A rule set lives in a file named ``<series>.rules.yaml``, for example
``mychecks.rules.yaml``, and you run it by passing the series name: ``fhir check mychecks``.

Further reading
---------------

For the full list of rules, their meaning, and how to author your own rule sets, see the
`Quality Control documentation <https://docs.fire.ly/projects/Simplifier/quality_control/quality_control.html>`_
on the Simplifier.net documentation site, including the
:ref:`minimal ruleset <simplifier_docs:qc>`.

Quality Control also runs well in a CI/CD pipeline, see :ref:`pipeline_integration`.
