.. _automating_simplifier_sync:

Automating Simplifier Sync
==========================
Firely Terminal is a .NET command line tool, so this works on any CI/CD platform that can install a
.NET tool — GitHub Actions, Azure DevOps, GitLab CI, Jenkins, Bitbucket Pipelines and others. Every
pipeline boils down to the same three steps: install the .NET SDK, run
``dotnet tool install --global Firely.Terminal``, then run the ``fhir`` commands you need.

Store your Simplifier credentials as secrets or protected variables in your CI/CD platform.
Never commit them to your repository.

All examples below use ``--strategy TakeLocal``, because in this scenario Git is the source of
truth: whatever is in the repository wins.

.. warning::
   These are **examples, not maintained or tested by Firely**. They are working starting points
   meant as inspiration — expect to adapt them to your repository layout and your platform, and to
   test them before you rely on them.

   Firely supports Firely Terminal, not your CI/CD platform. For the syntax, the runner images,
   the secret handling and everything else around the ``fhir`` commands, follow your platform's own
   documentation:
   `GitHub Actions <https://docs.github.com/actions>`_,
   `Azure Pipelines <https://learn.microsoft.com/azure/devops/pipelines>`_,
   `GitLab CI/CD <https://docs.gitlab.com/ee/ci/>`_.

GitHub Actions
--------------

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
------------

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

GitLab CI
---------

The same flow as a ``.gitlab-ci.yml`` job. Define ``SIMPLIFIER_USERNAME``, ``SIMPLIFIER_PASSWORD``
and ``SIMPLIFIER_PROJECT_URLKEY`` as CI/CD variables in **Settings > CI/CD > Variables**, and mark
the password both *Masked* and *Protected*. This job is manual; uncomment the second rule to run it
on every commit to ``main`` or ``develop``.

.. code-block:: yaml

    sync-simplifier:
      image: mcr.microsoft.com/dotnet/sdk:8.0
      rules:
        #Protected refs only: the credentials are not exposed to any other branch.
        - if: $CI_COMMIT_REF_PROTECTED == "true"
          when: manual          #play button in the pipeline view
        #- if: $CI_COMMIT_BRANCH =~ /^(main|develop)$/
      before_script:
        - dotnet tool install --global Firely.Terminal > /dev/null
        - export PATH="$PATH:$HOME/.dotnet/tools"
      script:
        - fhir -v
        - fhir login email=$SIMPLIFIER_USERNAME password=$SIMPLIFIER_PASSWORD
        - fhir project link $SIMPLIFIER_PROJECT_URLKEY --strategy TakeLocal
        - fhir project sync

.. warning::
   *Masked* only hides a variable in the job log; it does not control which jobs can read it. Any
   job on any branch can print it to a file or post it elsewhere, so a contributor who can push a
   branch can change ``.gitlab-ci.yml`` and take the password. *Protected* is the flag that matters:
   it restricts the variable to pipelines on protected branches and tags. Mark the password as both,
   and keep the sync job on protected refs as above — make ``main`` and ``develop`` protected
   branches if they are not already.

.. note::
   ``dotnet tool install --global`` puts ``fhir`` in ``$HOME/.dotnet/tools``, which is not on the
   ``PATH`` inside the SDK container. Exporting it — as above — is the fix for
   ``fhir: command not found``.

.. note::
   Every example runs ``fhir project link`` before every sync. A fresh CI checkout has no link to
   Simplifier yet, so the link has to be (re)established on every run.

To also validate your resources on every push, see :ref:`automating_quality_control`.
