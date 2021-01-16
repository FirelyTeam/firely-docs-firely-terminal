Managing packages
=================

Firely Terminal is a FHIR package manager. The following commands are available
to manage them.

Packages are the core distribution mechanism for sets of profiles.
Profiles and other conformance resources rarely live in isolation.
Together they form a use case, and they usually need other resources
from a national initiative, and they certainly need the FHIR core base
profiles.

In FHIR these sets are distributed according to the `FHIR package standard`_,
which conforms largely with the `NPM package standard`_. There are a few
additions and a few restrictions.

One of the core problems when distributing profiles, is choosing the
right version. And since profiles often reference eachother, you need
all the ones that are of the same release. For that, packages are ideal.

.. toctree::
    :maxdepth: 1
    :caption: Package Commands

    Managing-packages/Versions
    Managing-packages/Cache
    Managing-packages/SemVer
    Managing-packages/Install
    Managing-packages/Remove
    Managing-packages/Pack
    Managing-packages/Contents
    Managing-packages/Init
    Managing-packages/Restore
    Managing-packages/Scope
    Managing-packages/Canonicals
    Managing-packages/Find



.. _FHIR package standard: http://wiki.hl7.org/index.php?title=FHIR_NPM_Package_Spec
.. _NPM package standard: https://npmjs.org
