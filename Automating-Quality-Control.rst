.. _automating_quality_control:

Automating quality control
==========================

Firely Terminal is a .NET command line tool, so it runs on any CI/CD platform that can install a
.NET tool. That makes it a practical way to run :ref:`Quality Control <quality_control>` on your
FHIR project on every push or pull request: resolve the dependencies, validate every resource in the
project against its profiles, and fail the build when something is wrong.

This page covers what your repository needs for it to work, how to make the build actually fail, and
complete examples for GitHub Actions and Azure DevOps. The same commands run on your own machine, so
you can reproduce a red build locally.

Prerequisites
-------------

A ``package.json`` in the repository root
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

This is where ``fhir restore`` gets the FHIR version and the profile packages that validation
resolves against. Without it Firely Terminal has nothing to restore and cannot validate:

.. code-block:: json

    {
      "name": "my.ig",
      "version": "1.0.0",
      "fhirVersions": ["4.0.1"],
      "dependencies": {
        "hl7.fhir.r4.core": "4.0.1",
        "fhir.r4.ukcore.stu2": "2.0.1"
      }
    }

Keep ``dependencies`` in sync with what your profiles actually reference. An unresolved dependency
makes validation report false errors rather than fail loudly, which is the most confusing way for a
pipeline to be wrong. Failing early on a missing ``package.json`` is worth the three lines:

.. code-block:: Bash

     if [ ! -f package.json ]; then
       echo "package.json not found in the repo root - Firely Terminal needs it to resolve dependencies."
       exit 1
     fi

Choosing a rule series
^^^^^^^^^^^^^^^^^^^^^^

**The series you run decides whether validation happens at all.**

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
   * - ``<your series>``
     - Depends
     - Validates only the paths its own ``validate`` action lists.

.. warning::
   Running ``fhir check free`` in CI to avoid the license requirement means your resources are **not
   being validated**. Use ``minimal`` or better for a validation gate.

A series is a **name, not a path**. Firely Terminal looks for ``<series>.rules.yaml`` in the project
root, so ``fhir check myrules`` runs ``myrules.rules.yaml``. This is the most common mistake when
moving a working local setup into a pipeline.

Simplifier credentials and license
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Quality Control is a licensed feature, so the pipeline has to log in before it can run ``fhir check``
with a validating series. Store the credentials as secrets or protected variables in your CI/CD
platform and never commit them:

.. code-block:: Bash

     > fhir login email=$SIMPLIFIER_USERNAME password=$SIMPLIFIER_PASSWORD

Note this is the Simplifier **email address**, not a username. See :ref:`quality_control` for what
the license covers.

Projects that use SUSHI
^^^^^^^^^^^^^^^^^^^^^^^

SUSHI keeps its dependencies in ``sushi-config.yaml`` and does not emit a root ``package.json``, so
Firely Terminal has nothing to restore. On GitHub the official action handles this for you (see
below). On any other platform, generate a ``package.json`` from the SUSHI config before the restore
step:

.. code-block:: yaml

    - script: |
        set -euo pipefail
        npm install -g fsh-sushi
        sushi build .
        python3 - <<'PY'
        import json, yaml
        c = yaml.safe_load(open('sushi-config.yaml'))
        deps = {k: (v if isinstance(v, str) else v.get('version'))
                for k, v in (c.get('dependencies') or {}).items()}
        fhir_version = c.get('fhirVersion', '4.0.1')
        json.dump({'name': c.get('id', 'temp.package'),
                   'version': str(c.get('version', '0.0.0')),
                   'fhirVersions': [fhir_version] if isinstance(fhir_version, str) else fhir_version,
                   'dependencies': deps}, open('package.json', 'w'), indent=2)
        PY
      displayName: 'SUSHI build and package.json from sushi-config.yaml'

Two things to watch:

* ``sushi build`` writes to ``fsh-generated/resources/``, so your rule series' ``parse`` action has
  to include that path or it will validate nothing.
* ``sushi-config.yaml`` dependencies come in both short form (``hl7.fhir.us.core: 3.1.0``) and long
  form (with ``version:`` / ``uri:``); the snippet above handles both.

Failing the build
-----------------

.. important::
   ``fhir check`` **exits 0 even when it reports errors.** It prints ``FAIL:`` with the issue counts
   and still returns a success exit code. A non-zero exit code means the check could not run at all —
   no license, an unknown series, or not a project folder.

That is deliberate: a broken setup fails loudly instead of quietly reporting "no issues". But it
means ``fhir check`` on its own is not a gate. Add ``--fail`` to turn error-level findings into a
non-zero exit code:

.. code-block:: Bash

     > fhir check myrules             #reports issues, always exits 0
     > fhir check myrules --fail      #exits non-zero when there are errors

One flag is enough for most pipelines. If you also want a report or an artifact out of the run, see
:ref:`the full example <full_validation_example>`, which gates differently and explains why.

.. _firely_terminal_pipeline_action:

Option 1: the Firely Terminal pipeline action (GitHub)
------------------------------------------------------

If you are on GitHub, start here. We maintain
`FirelyTeam/firely-terminal-pipeline <https://github.com/FirelyTeam/firely-terminal-pipeline>`_, a
composite action that wraps the whole sequence — dependency resolution, optional SUSHI build,
``fhir check --fail``, and optionally the official HL7 Java validator as a second opinion.

.. code-block:: yaml

    name: Firely Validation
    on:
      push:
        branches: [ main ]
      pull_request:
        branches: [ main ]
      workflow_dispatch:

    jobs:
      CI_FHIR_VALIDATION:
        runs-on: ubuntu-latest
        steps:
          - uses: actions/checkout@v4

          - name: Setup .NET SDK
            uses: actions/setup-dotnet@v4
            with:
              dotnet-version: 8.0.x

          # Only needed when JAVA_VALIDATION_ENABLED is true
          - name: Setup Java JDK
            uses: actions/setup-java@v4
            with:
              distribution: 'microsoft'
              java-version: '21'

          - name: Firely.Terminal (GitHub Actions)
            uses: FirelyTeam/firely-terminal-pipeline@v0.8.17
            with:
              PATH_TO_CONFORMANCE_RESOURCES: Conformance-resources
              PATH_TO_EXAMPLES: Examples
              PATH_TO_QUALITY_CONTROL_RULES: myrules
              DOTNET_VALIDATION_ENABLED: true
              JAVA_VALIDATION_ENABLED: true
              SIMPLIFIER_USERNAME: ${{ secrets.SIMPLIFIER_USERNAME }}
              SIMPLIFIER_PASSWORD: ${{ secrets.SIMPLIFIER_PASSWORD }}

Worth knowing before you adopt it:

* It is a **composite action**, so your job installs the toolchain: ``actions/setup-dotnet`` always,
  and ``actions/setup-java`` when you enable the Java validator. The action fails fast if either is
  missing.
* **Pin an exact tag.** Releases are ``vX.Y.Z`` and there is no floating major tag to follow.
* It runs on Linux GitHub runners only. It cannot be consumed from Azure DevOps or GitLab — for those,
  use option 2.
* The signal is the job's pass/fail status plus the log. The action publishes no artifacts, no job
  summary and no pull request annotations. If you need those, use option 2.
* ``SUSHI_ENABLED`` and ``SUSHI_USE_CONFIG_DEPENDENCIES`` cover FSH projects, so you do not need the
  converter snippet above.

The full input list lives in the repository's ``action.yml``, which is the authoritative source — the
README's option table lags behind it.

Option 2: your own pipeline (any platform)
------------------------------------------

Every platform boils down to the same steps: install the .NET SDK, install Firely Terminal, log in,
restore, check. Rolling it yourself is the way to go when you are not on GitHub, or when you want
control over reporting.

.. warning::
   Unlike the pipeline action above, the examples in this section are **not maintained or tested by
   Firely**. They are working starting points meant as inspiration — expect to adapt them to your
   repository layout, your rule series and your platform, and to test them before you rely on them
   as a gate.

The smallest pipeline that gates
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

**GitHub Actions** — ``.github/workflows/validate.yml``:

.. code-block:: yaml

    name: FHIR Validation
    on:
      push:
        branches: [ main ]
      pull_request:
        branches: [ main ]

    jobs:
      validate:
        runs-on: ubuntu-latest
        steps:
          - uses: actions/checkout@v4

          - uses: actions/setup-dotnet@v4
            with:
              dotnet-version: 8.0.x

          - name: Install Firely Terminal
            run: |
              dotnet tool install --global Firely.Terminal --version 3.5.0
              fhir -v

          - name: Validate
            env:
              SIMPLIFIER_USERNAME: ${{ secrets.SIMPLIFIER_USERNAME }}
              SIMPLIFIER_PASSWORD: ${{ secrets.SIMPLIFIER_PASSWORD }}
            run: |
              set -euo pipefail
              fhir login email=$SIMPLIFIER_USERNAME password=$SIMPLIFIER_PASSWORD
              fhir restore
              fhir check myrules --fail

**Azure DevOps** — one pipeline definition, same sequence:

.. code-block:: yaml

    trigger:
      branches:
        include: [ main ]
    pr:
      branches:
        include: [ main ]

    pool:
      vmImage: ubuntu-latest

    steps:
      - task: UseDotNet@2
        displayName: Setup .NET SDK
        inputs:
          version: 8.0.x

      - script: |
          set -euo pipefail
          dotnet tool install --global Firely.Terminal --version 3.5.0
          fhir -v
        displayName: Install Firely Terminal

      - script: |
          set -euo pipefail
          fhir login email=$SIMPLIFIER_USERNAME password=$SIMPLIFIER_PASSWORD
          fhir restore
          fhir check myrules --fail
        displayName: Validate
        env:
          SIMPLIFIER_USERNAME: $(SIMPLIFIER_USERNAME)
          SIMPLIFIER_PASSWORD: $(SIMPLIFIER_PASSWORD)

Define ``SIMPLIFIER_USERNAME`` and ``SIMPLIFIER_PASSWORD`` as GitHub repository secrets or as
**secret** Azure pipeline variables.

.. _full_validation_example:

A full example with a report and an artifact
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The lean version tells you the build failed; you then read the log to find out why. This version
turns that log into a markdown report attached to the run summary, and publishes the log itself as
an artifact — which is what you want when the pipeline runs on pull requests, or when someone else
has to act on the findings.

The report is built by parsing ``fhir check``'s console output, because **only the console output
names the file each issue came from**. ``fhir check --push`` gives you a machine-readable
``OperationOutcome``, but it carries no file paths, which is the first thing anyone asks about a
failed validation run.

It works like this:

#. Install the .NET SDK and Firely Terminal, pinned to a version.
#. Log in to Simplifier.
#. Fail early if ``package.json`` is missing, then ``fhir config regenerate on`` and ``fhir restore``.
   ``regenerate on`` forces snapshots to be rebuilt from the differentials instead of trusting
   snapshots committed to the repository, so CI validates what your differentials actually say.
#. Run ``fhir check <series>`` and ``tee`` the output to ``validation-check.log``.
#. Parse that log into a markdown report — counts per rule, the files with the most issues, then
   every issue grouped by rule — and **exit 1 if there are errors or fatals**. This step is the gate.
#. Attach the report to the run summary and publish the log, both unconditionally so they survive a
   failing run.

.. warning::
   The report parses console output, which is not a stable contract. If a future Firely Terminal
   release changes the layout of ``fhir check``'s output, the report needs adjusting — the counts in
   the ``FAIL:`` summary line are the quickest way to spot that the parser has drifted.

**Azure DevOps:**

.. code-block:: yaml

    # Firely Terminal FHIR resource validation.
    # Requires a package.json in the repo root. Define SIMPLIFIER_USERNAME and
    # SIMPLIFIER_PASSWORD as *secret* pipeline variables, or in a linked variable group.

    trigger:
      branches:
        include: [ main, develop, release/* ]
    pr:
      branches:
        include: [ main ]

    pool:
      vmImage: ubuntu-latest

    variables:
      FIRELY_TERMINAL_VERSION: 3.5.0
      # Rule series name = the "<series>.rules.yaml" filename prefix, not a path.
      QC_RULE_SERIES: myrules

    steps:
      - task: UseDotNet@2
        displayName: Setup .NET SDK
        inputs:
          version: 8.0.x

      - script: |
          set -euo pipefail
          dotnet tool install --global Firely.Terminal --version $(FIRELY_TERMINAL_VERSION)
          fhir -v
        displayName: Install Firely Terminal

      - script: fhir login email=$SIMPLIFIER_USERNAME password=$SIMPLIFIER_PASSWORD
        displayName: Simplifier login
        env:
          SIMPLIFIER_USERNAME: $(SIMPLIFIER_USERNAME)
          SIMPLIFIER_PASSWORD: $(SIMPLIFIER_PASSWORD)

      - script: |
          set -euo pipefail
          if [ ! -f package.json ]; then
            echo "package.json not found in the repo root. Firely Terminal needs it to resolve dependencies."
            exit 1
          fi
          fhir config regenerate on
          fhir restore
        displayName: FHIR dependency restore

      - script: |
          set -euo pipefail
          # Exits 0 on findings, non-zero when it can't run. Only the console output names the
          # file per issue, so the log is the report source.
          fhir check $(QC_RULE_SERIES) | tee validation-check.log
        displayName: Run quality control checks

      - script: |
          set -euo pipefail
          python3 - <<'PY'
          import collections, sys

          # fhir check prints, per issue: the rule description, the file, "Error:/Warning: <message>",
          # an optional "At: <fhirpath>" and a "<system>|<code>" line. Project-level rules report
          # without a file. A file line is a bare path; a rule line is a sentence with spaces.
          SEV = {'Fatal': 'fatal', 'Error': 'error', 'Warning': 'warning',
                 'Info': 'information', 'Information': 'information'}
          SKIP = ('FAIL', 'STATUS', 'SUCCESS', 'Rule completed')
          is_path = lambda s: bool(s.strip()) and ' ' not in s.strip()

          issues, rule, prev = [], '(no rule)', ''
          for ln in open('validation-check.log', encoding='utf-8').read().splitlines():
              if ln.strip() and not ln.startswith(' '):
                  key = ln.split(':', 1)[0]
                  if key in SEV:
                      issues.append({'rule': rule, 'sev': SEV[key], 'code': '', 'loc': '',
                                     'file': prev.strip() if is_path(prev) else '-',
                                     'msg': ln.split(': ', 1)[-1]})
                  elif ln.startswith('At: ') and issues:
                      issues[-1]['loc'] = ln[4:]
                  elif ln.startswith(('http://', 'https://')) and issues:
                      issues[-1]['code'] = ln.rsplit('|', 1)[-1]
                  elif not ln.startswith(SKIP) and not is_path(ln):
                      rule = ln
              prev = ln

          ORDER = ['fatal', 'error', 'warning', 'information']
          cell = lambda s: s.replace('|', '\\|')
          by_sev = collections.Counter(i['sev'] for i in issues)
          out = ['# FHIR Validation Report', '']
          if not issues:
              out.append('No validation issues found.')
          else:
              files = collections.Counter(i['file'] for i in issues if i['file'] != '-')
              rules = list(dict.fromkeys(i['rule'] for i in issues))
              out += ['**%s** in %d file(s), from %d rule(s).' % (
                      ', '.join('%d %s%s' % (by_sev[s], s, '' if by_sev[s] == 1 else 's')
                                for s in ORDER if by_sev[s]),
                      len(files), len(rules)), '',
                      '| Rule | Errors | Warnings | Info |', '|---|--:|--:|--:|']
              for r in rules:
                  c = collections.Counter(i['sev'] for i in issues if i['rule'] == r)
                  out.append('| %s | %d | %d | %d |' % (cell(r), c['error'] + c['fatal'],
                                                        c['warning'], c['information']))
              top = files.most_common(10)
              out += ['', '## Files with the most issues', '']
              if len(files) > len(top):
                  out += ['Showing the top %d of %d files.' % (len(top), len(files)), '']
              out += ['| File | Issues |', '|---|--:|']
              out += ['| `%s` | %d |' % (f, c) for f, c in top]
              out += ['', '## Issues per rule']
              for r in rules:
                  out += ['', '### %s' % r, '',
                          '| Severity | File | Code | Location | Message |', '|---|---|---|---|---|']
                  for i in sorted((i for i in issues if i['rule'] == r),
                                  key=lambda i: (ORDER.index(i['sev']), i['file'])):
                      out.append('| %s | `%s` | `%s` | `%s` | %s |' % (
                          i['sev'], i['file'], i['code'], i['loc'] or '-', cell(i['msg'])))

          report = '\n'.join(out) + '\n'
          open('validation-report.md', 'w', encoding='utf-8').write(report)
          print(report)
          sys.exit(1 if by_sev['error'] + by_sev['fatal'] else 0)
          PY
        displayName: Generate validation report
        # This step is the pass/fail gate: it exits 1 when there are errors or fatals.

      - script: echo "##vso[task.uploadsummary]$(System.DefaultWorkingDirectory)/validation-report.md"
        displayName: Attach report to build summary
        condition: succeededOrFailed()

      - task: PublishPipelineArtifact@1
        displayName: Publish validation artifacts
        condition: succeededOrFailed()
        inputs:
          targetPath: validation-check.log
          artifact: fhir-validation-results

**GitHub Actions** — the same pipeline. Only the reporting plumbing differs:
``$GITHUB_STEP_SUMMARY`` instead of ``task.uploadsummary``, ``actions/upload-artifact`` instead of
``PublishPipelineArtifact@1``, and ``if: always()`` instead of ``condition: succeededOrFailed()``.

.. code-block:: yaml

    name: FHIR Validation
    on:
      push:
        branches: [ main, develop, 'release/*' ]
      pull_request:
        branches: [ main ]
      workflow_dispatch:

    env:
      FIRELY_TERMINAL_VERSION: 3.5.0
      # Rule series name = the "<series>.rules.yaml" filename prefix, not a path.
      QC_RULE_SERIES: myrules

    jobs:
      validate:
        runs-on: ubuntu-latest
        steps:
          - uses: actions/checkout@v4

          - name: Setup .NET SDK
            uses: actions/setup-dotnet@v4
            with:
              dotnet-version: 8.0.x

          - name: Install Firely Terminal
            run: |
              set -euo pipefail
              dotnet tool install --global Firely.Terminal --version "$FIRELY_TERMINAL_VERSION"
              fhir -v

          - name: Simplifier login
            env:
              SIMPLIFIER_USERNAME: ${{ secrets.SIMPLIFIER_USERNAME }}
              SIMPLIFIER_PASSWORD: ${{ secrets.SIMPLIFIER_PASSWORD }}
            run: fhir login email=$SIMPLIFIER_USERNAME password=$SIMPLIFIER_PASSWORD

          - name: FHIR dependency restore
            run: |
              set -euo pipefail
              if [ ! -f package.json ]; then
                echo "package.json not found in the repo root. Firely Terminal needs it to resolve dependencies."
                exit 1
              fi
              fhir config regenerate on
              fhir restore

          - name: Run quality control checks
            run: |
              set -euo pipefail
              # Exits 0 on findings, non-zero when it can't run. Only the console output names the
              # file per issue, so the log is the report source.
              fhir check "$QC_RULE_SERIES" | tee validation-check.log

          - name: Generate validation report
            # This step is the pass/fail gate: it exits 1 when there are errors or fatals.
            run: |
              set -euo pipefail
              python3 - <<'PY'
              import collections, sys

              # fhir check prints, per issue: the rule description, the file, "Error:/Warning: <message>",
              # an optional "At: <fhirpath>" and a "<system>|<code>" line. Project-level rules report
              # without a file. A file line is a bare path; a rule line is a sentence with spaces.
              SEV = {'Fatal': 'fatal', 'Error': 'error', 'Warning': 'warning',
                     'Info': 'information', 'Information': 'information'}
              SKIP = ('FAIL', 'STATUS', 'SUCCESS', 'Rule completed')
              is_path = lambda s: bool(s.strip()) and ' ' not in s.strip()

              issues, rule, prev = [], '(no rule)', ''
              for ln in open('validation-check.log', encoding='utf-8').read().splitlines():
                  if ln.strip() and not ln.startswith(' '):
                      key = ln.split(':', 1)[0]
                      if key in SEV:
                          issues.append({'rule': rule, 'sev': SEV[key], 'code': '', 'loc': '',
                                         'file': prev.strip() if is_path(prev) else '-',
                                         'msg': ln.split(': ', 1)[-1]})
                      elif ln.startswith('At: ') and issues:
                          issues[-1]['loc'] = ln[4:]
                      elif ln.startswith(('http://', 'https://')) and issues:
                          issues[-1]['code'] = ln.rsplit('|', 1)[-1]
                      elif not ln.startswith(SKIP) and not is_path(ln):
                          rule = ln
                  prev = ln

              ORDER = ['fatal', 'error', 'warning', 'information']
              cell = lambda s: s.replace('|', '\\|')
              by_sev = collections.Counter(i['sev'] for i in issues)
              out = ['# FHIR Validation Report', '']
              if not issues:
                  out.append('No validation issues found.')
              else:
                  files = collections.Counter(i['file'] for i in issues if i['file'] != '-')
                  rules = list(dict.fromkeys(i['rule'] for i in issues))
                  out += ['**%s** in %d file(s), from %d rule(s).' % (
                          ', '.join('%d %s%s' % (by_sev[s], s, '' if by_sev[s] == 1 else 's')
                                    for s in ORDER if by_sev[s]),
                          len(files), len(rules)), '',
                          '| Rule | Errors | Warnings | Info |', '|---|--:|--:|--:|']
                  for r in rules:
                      c = collections.Counter(i['sev'] for i in issues if i['rule'] == r)
                      out.append('| %s | %d | %d | %d |' % (cell(r), c['error'] + c['fatal'],
                                                            c['warning'], c['information']))
                  top = files.most_common(10)
                  out += ['', '## Files with the most issues', '']
                  if len(files) > len(top):
                      out += ['Showing the top %d of %d files.' % (len(top), len(files)), '']
                  out += ['| File | Issues |', '|---|--:|']
                  out += ['| `%s` | %d |' % (f, c) for f, c in top]
                  out += ['', '## Issues per rule']
                  for r in rules:
                      out += ['', '### %s' % r, '',
                              '| Severity | File | Code | Location | Message |', '|---|---|---|---|---|']
                      for i in sorted((i for i in issues if i['rule'] == r),
                                      key=lambda i: (ORDER.index(i['sev']), i['file'])):
                          out.append('| %s | `%s` | `%s` | `%s` | %s |' % (
                              i['sev'], i['file'], i['code'], i['loc'] or '-', cell(i['msg'])))

              report = '\n'.join(out) + '\n'
              open('validation-report.md', 'w', encoding='utf-8').write(report)
              print(report)
              sys.exit(1 if by_sev['error'] + by_sev['fatal'] else 0)
              PY

          - name: Attach report to run summary
            if: always()
            run: cat validation-report.md >> "$GITHUB_STEP_SUMMARY"

          - name: Publish validation artifacts
            if: always()
            uses: actions/upload-artifact@v4
            with:
              name: fhir-validation-results
              path: validation-check.log

.. note::
   The inline Python is duplicated in both definitions on purpose, so the two stay comparable. In
   your own repository you will probably want it in a file — ``scripts/validation-report.py`` — and
   the step reduced to ``python3 scripts/validation-report.py``.

Why the full example does not use ``--fail``
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Under ``set -euo pipefail``, ``fhir check --fail`` makes the check step itself fail, so the report
step never runs and you get no report on exactly the builds that need one. When a pipeline produces
a report, let ``fhir check`` exit 0 and make the report step the gate.

That is also why the summary and artifact steps carry ``condition: succeededOrFailed()`` and
``if: always()``: without them, the report would only be published for builds that passed.

Running the same checks locally
-------------------------------

The pipeline runs nothing you cannot run yourself. From your project folder:

.. code-block:: Bash

     > fhir login email=<your email> password=<your password>
     > fhir restore
     > fhir check myrules              #the same validation the pipeline runs
     > fhir check myrules --fail       #and the same exit code it gates on

To reproduce the full example locally, including the markdown report, the script below runs the
identical sequence and exits 1 exactly where CI would fail. Save it as ``Test-Validation.ps1``:

.. code-block:: PowerShell

    [CmdletBinding()]
    param(
        [string]$Series = 'myrules',
        [string]$TerminalVersion = '3.5.0',
        [switch]$Login,     # prompts for Simplifier credentials
        [switch]$Restore    # re-resolve package.json dependencies
    )

    $ErrorActionPreference = 'Stop'

    if (-not (Test-Path package.json)) {
        throw "package.json not found. Run this from the repo root."
    }

    if (-not (Get-Command fhir -ErrorAction SilentlyContinue)) {
        dotnet tool install --global Firely.Terminal --version $TerminalVersion
    }
    fhir -v

    if ($Login) {
        $cred = Get-Credential -Message 'Simplifier.net credentials'
        fhir login "email=$($cred.UserName)" "password=$($cred.GetNetworkCredential().Password)"
        if ($LASTEXITCODE -ne 0) { throw 'Simplifier login failed.' }
    }

    if ($Restore) {
        fhir config regenerate on
        fhir restore
        if ($LASTEXITCODE -ne 0) { throw 'fhir restore failed - see the [Missing] packages above.' }
    }

    fhir check $Series | Tee-Object validation-check.log
    if ($LASTEXITCODE -ne 0) {
        throw "fhir check could not run (exit $LASTEXITCODE). A licensed series needs an active license - try -Login, or -Series free."
    }

    # The same report the pipeline builds, so you can preview the run summary locally.
    # fhir check prints, per issue: the rule description, the file, "Error:/Warning: <message>", an
    # optional "At: <fhirpath>" and a "<system>|<code>" line. Project-level rules report without a
    # file. A file line is a bare path; a rule line is a sentence with spaces.
    $sev = @{ Fatal = 'fatal'; Error = 'error'; Warning = 'warning'; Info = 'information'; Information = 'information' }
    $order = @{ fatal = 0; error = 1; warning = 2; information = 3 }
    $isPath = { param($s) $s.Trim() -and $s.Trim() -notmatch '\s' }

    $lines = Get-Content validation-check.log
    $issues = [System.Collections.ArrayList]::new()
    $rule = '(no rule)'
    $prev = ''
    foreach ($ln in $lines) {
        if ($ln.Trim() -and -not $ln.StartsWith(' ')) {
            $key = ($ln -split ':', 2)[0]
            if ($sev.ContainsKey($key)) {
                [void]$issues.Add([pscustomobject]@{
                    rule = $rule
                    sev  = $sev[$key]
                    file = if (& $isPath $prev) { $prev.Trim() } else { '-' }
                    msg  = ($ln -split ': ', 2)[-1]
                    loc  = ''
                    code = ''
                })
            } elseif ($ln.StartsWith('At: ') -and $issues.Count) {
                $issues[-1].loc = $ln.Substring(4)
            } elseif ($ln -match '^https?://' -and $issues.Count) {
                $issues[-1].code = ($ln -split '\|')[-1]
            } elseif ($ln -notmatch '^(FAIL|STATUS|SUCCESS|Rule completed)' -and -not (& $isPath $ln)) {
                $rule = $ln
            }
        }
        $prev = $ln
    }

    $md = @('# FHIR Validation Report', '')
    $counts = $issues | Group-Object sev | Sort-Object { $order[$_.Name] }
    if (-not $issues) {
        $md += 'No validation issues found.'
    } else {
        $files = $issues | Where-Object file -ne '-' | Group-Object file | Sort-Object Count -Descending
        $rules = $issues.rule | Select-Object -Unique
        $md += '**{0}** in {1} file(s), from {2} rule(s).' -f
               (($counts | ForEach-Object { "$($_.Count) $($_.Name)$(if ($_.Count -ne 1) { 's' })" }) -join ', '),
               $files.Count, $rules.Count
        $md += @('', '| Rule | Errors | Warnings | Info |', '|---|--:|--:|--:|')
        foreach ($r in $rules) {
            $sub = $issues | Where-Object rule -eq $r
            $md += '| {0} | {1} | {2} | {3} |' -f ($r -replace '\|', '\|'),
                   @($sub | Where-Object sev -in 'error', 'fatal').Count,
                   @($sub | Where-Object sev -eq 'warning').Count,
                   @($sub | Where-Object sev -eq 'information').Count
        }
        $top = @($files | Select-Object -First 10)
        $md += @('', '## Files with the most issues', '')
        if ($files.Count -gt $top.Count) { $md += @("Showing the top $($top.Count) of $($files.Count) files.", '') }
        $md += @('| File | Issues |', '|---|--:|')
        $md += $top | ForEach-Object { "| ``$($_.Name)`` | $($_.Count) |" }
        $md += @('', '## Issues per rule')
        foreach ($r in $rules) {
            $md += @('', "### $r", '', '| Severity | File | Code | Location | Message |', '|---|---|---|---|---|')
            $md += $issues | Where-Object rule -eq $r | Sort-Object { $order[$_.sev] }, file | ForEach-Object {
                $loc = if ($_.loc) { $_.loc } else { '-' }
                "| $($_.sev) | ``$($_.file)`` | ``$($_.code)`` | ``$loc`` | $($_.msg -replace '\|', '\|') |"
            }
        }
    }
    $md -join "`n" | Set-Content validation-report.md -Encoding utf8

    Write-Host "`nWrote validation-report.md (the report CI attaches to the build summary)." -ForegroundColor Cyan
    foreach ($c in $counts) { Write-Host ("  {0,-12} {1}" -f $c.Name, $c.Count) }
    if ($issues.sev -contains 'error' -or $issues.sev -contains 'fatal') {
        Write-Host 'CI would FAIL this commit (errors present).' -ForegroundColor Red
        exit 1
    }

.. code-block:: PowerShell

    PS> .\Test-Validation.ps1                  # uses an existing login and restore
    PS> .\Test-Validation.ps1 -Login -Restore  # first run, or after package.json changed
    PS> .\Test-Validation.ps1 -Series free     # no license needed, but does not validate

``-Login`` and ``-Restore`` are opt-in so that repeat runs stay fast — a restore only matters when
``package.json`` changes.

.. note::
   The PowerShell script reimplements the pipeline's Python rather than sharing it, so that it runs
   on a plain Windows box with nothing else installed. If you change the report layout, change both.

Adapting the examples
---------------------

* **Report without gating.** Drop the ``sys.exit(...)`` line at the end of the report step. The
  report is still attached to the run, but findings no longer fail the build.
* **Treat warnings as errors.** Add ``by_sev['warning']`` to the same ``sys.exit`` expression.
* **Other platforms.** GitLab CI, Jenkins, Bitbucket Pipelines and the rest need the same three
  things: a .NET SDK, ``dotnet tool install --global Firely.Terminal``, and the ``fhir`` commands
  above. Only the secret handling and the report plumbing are platform-specific.
* **Runner requirements.** The examples use ``set -euo pipefail``, ``tee`` and ``python3``, so they
  assume a Linux agent. ``ubuntu-latest`` on both GitHub and Azure DevOps has all three.
* **A second opinion.** To also run the official HL7 Java validator, the
  :ref:`pipeline action <firely_terminal_pipeline_action>` does it with one input on GitHub;
  elsewhere, download ``validator_cli.jar`` and add a step of your own.

See :ref:`automating_simplifier_sync` for other things worth automating with Firely Terminal, such
as keeping a Simplifier project in sync with your repository.
