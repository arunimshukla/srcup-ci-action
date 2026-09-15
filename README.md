## srcup-ci-action

Latest version: [v0.0.2](https://github.com/Dedaub/srcup-ci-action/releases/tag/v0.0.2)

This action wraps Dedaub's [srcup](https://github.com/Dedaub/srcup) tool as a github action so that it can be easily used
in workflows

### Usage

See [action.yml](https://github.com/Dedaub/srcup-ci-action/blob/main/action.yml)

### Inputs

The action expects a number of inputs:

- name: The name of the project. If one is not provided the name of the directory containing the project will be used
- comment: (optional) An optional short comment (commit message) describing the changes that the version of the uploaded project contains
- init: (optional) If is set to true then it is assumed that this is the first (initial) version of the project uploaded. Leave empty or undefined otherwise
- owner: (optional) If you are submitting for a project that is not owned by you, the username of the owner
- framework: (optional) The project's framework. See the documentation in [srcup](https://github.com/Dedaub/srcup)
- api-key: (optional) The key is optional if you configured the runner to use a configuration file. Otherwise needs to be provided
- cache: (optional) If cache should be used. See the documentation in [srcup](https://github.com/Dedaub/srcup)
- location: Location of the project's sources. Defaults to the current directory

### Outputs

The action produces two outputs: `project_id` and `version_id`

### Example

The following steps upload a project, retrieve its analysis summary with
`srcwarnings-ci-action`, and write the report to the workflow's job summary.
Add them after checking out the repository and installing the project's dependencies.
Replace `staking-v0.1` with your project directory and configure the
`WATCHDOG_SECRET` repository secret with your Dedaub API key.

The upload action exposes underscore-separated output names (`project_id` and
`version_id`). The warnings action accepts those values through hyphen-separated
inputs (`project-id` and `version-id`).

```yaml
- name: Run srcup
  id: srcup
  uses: dedaub/srcup-ci-action@v0.0.2
  with:
    name: "cool-project-name"
    api-key: ${{ secrets.WATCHDOG_SECRET }}
    framework: "Hardhat"
    location: staking-v0.1
- name: Get analysis summary
  id: get_project_report
  uses: dedaub/srcwarnings-ci-action@v0.0.1
  with:
    project-id: ${{ steps.srcup.outputs.project_id }}
    version-id: ${{ steps.srcup.outputs.version_id }}
    api-key: ${{ secrets.WATCHDOG_SECRET }}
- name: Print the info
  env:
    PROJECT_REPORT: ${{ steps.get_project_report.outputs.project_report }}
  run: |
    printf '%s\n' "$PROJECT_REPORT"
    printf '%s\n' "$PROJECT_REPORT" >> "$GITHUB_STEP_SUMMARY"
  shell: bash
```
