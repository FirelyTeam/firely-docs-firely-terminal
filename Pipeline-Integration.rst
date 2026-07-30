.. _pipeline_integration:

Pipeline integration
====================

Firely Terminal is a .NET command line tool, so it runs on any CI/CD platform that can install a
.NET tool: GitHub Actions, Azure DevOps, GitLab CI, Jenkins, Bitbucket Pipelines, and others.
Every pipeline boils down to the same three steps:

1. Install the .NET SDK.
2. Install Firely Terminal: ``dotnet tool install --global Firely.Terminal``.
3. Run the ``fhir`` commands you need, for example :ref:`Quality Control <quality_control>` or a
   :ref:`Simplifier sync <working_with_simplifier>`.

Store your Simplifier credentials as secrets or protected variables in your CI/CD platform.
Never commit them to your repository.

Quality Control in a pipeline
-----------------------------

For validating and quality-checking an Implementation Guide, we maintain a ready-made GitHub
Actions workflow that also wraps the HL7 Java Validator. See our
`firely-terminal-pipeline <https://github.com/FirelyTeam/firely-terminal-pipeline>`_ repository on
how to set it up and deploy it. If you are on another platform, that repository is still a useful
reference for which commands to run.

Automating a Simplifier sync
----------------------------

There is no two-way synchronization between your Git host and Simplifier.net (see
:ref:`working_with_simplifier`). A pipeline is the usual way to close that gap: on every push to
your main branch, send the repository contents to the corresponding Simplifier project.

Both examples below use ``--strategy TakeLocal``, because in this scenario Git is the source of
truth: whatever is in the repository wins.

GitHub Actions
^^^^^^^^^^^^^^

Add the following as ``.github/workflows/sync-simplifier.yaml`` and configure
``SIMPLIFIER_USERNAME``, ``SIMPLIFIER_PASSWORD`` and ``SIMPLIFIER_PROJECT_URLKEY`` as repository
secrets. This example is triggered manually; uncomment the ``push`` trigger to run it on every
commit to ``main`` or ``develop``.

.. code-block:: yaml

    name: Simplifier Sync using Firely Terminal
    on:
      workflow_dispatch: {}
      #push:
        #branches: [ main, develop ]

    jobs:
      sync-simplifier:
        name: Sync to Simplifier
        runs-on: ubuntu-latest
        steps:
          - name: Checkout
            uses: actions/checkout@v4

          - name: Setup .NET
            uses: actions/setup-dotnet@v4
            with:
              dotnet-version: '8.0.x'

          - name: Install Firely.Terminal
            run: |
              if ! command -v fhir &> /dev/null; then
                dotnet tool install --global Firely.Terminal > /dev/null
              fi

          - name: Check Firely Terminal Version
            run: fhir -v

          - name: Simplifier login and sync
            env:
              SIMPLIFIER_USERNAME: ${{ secrets.SIMPLIFIER_USERNAME }}
              SIMPLIFIER_PASSWORD: ${{ secrets.SIMPLIFIER_PASSWORD }}
              SIMPLIFIER_PROJECT_URLKEY: ${{ secrets.SIMPLIFIER_PROJECT_URLKEY }}
            run: |
              fhir login email=$SIMPLIFIER_USERNAME password=$SIMPLIFIER_PASSWORD
              fhir project link $SIMPLIFIER_PROJECT_URLKEY --strategy TakeLocal
              fhir project sync

Azure DevOps
^^^^^^^^^^^^

The same flow as an Azure Pipelines definition. Define ``SIMPLIFIER_USERNAME``,
``SIMPLIFIER_PASSWORD`` and ``SIMPLIFIER_PROJECTURLKEY`` as pipeline variables, and mark the
password as secret.

.. code-block:: yaml

    trigger:
    - main

    pool:
      vmImage: ubuntu-latest

    steps:
    - task: UseDotNet@2
      inputs:
        version: '8.x'

    - script: |
        if ! command -v fhir &> /dev/null; then
          dotnet tool install --global Firely.Terminal > /dev/null
        fi
      displayName: 'Install Firely.Terminal'

    - script: fhir -v
      displayName: 'Check Firely Terminal Version'

    - script: |
        fhir login email=$SIMPLIFIER_USERNAME password=$SIMPLIFIER_PASSWORD
        fhir project link $(SIMPLIFIER_PROJECTURLKEY) --strategy TakeLocal
        fhir project sync
      displayName: 'Simplifier login and sync'
      env:
        SIMPLIFIER_USERNAME: $(SIMPLIFIER_USERNAME)
        SIMPLIFIER_PASSWORD: $(SIMPLIFIER_PASSWORD)

.. note::
   Both examples run ``fhir project link`` before every sync. A fresh CI checkout has no link to
   Simplifier yet, so the link has to be (re)established on every run.
