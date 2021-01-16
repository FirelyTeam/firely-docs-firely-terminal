Expand
------

.. image:: https://img.shields.io/badge/Simplifier.net%20plan-Professional%20and%20higher-green
    :alt: Simplifier.net plan: Professional and higher
    :target: https://simplifier.net/pricing


Expand takes the ValueSet on top of the stack, and uses the current
:ref:`Scope` to expand all the codes in the ValueSet The resulting
ValueSet is then placed back on top of the stack.

Usage
~~~~~

.. code-block:: Bash

   > fhir expand

License
~~~~~~~

The expand command can only be used by licensed customers of
Simplifier.

Limits
~~~~~~

The current expand command is limited to regular ValueSets, so special
cases like SNOMETD need to be done on a FHIR server. The expand command
is cucrrent limited to 1.000.000 codes. That should be enough for most
ValueSets. Beware that it can take a long time though.