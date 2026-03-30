# veracode-local-sca-results

A GitHub Copilot skill for interpreting [Veracode local SCA scan](https://docs.veracode.com/r/Veracode_SCA) JSON results. It summarises dependency vulnerabilities (SCA) and IaC/Dockerfile misconfigurations, and provides prioritised remediation guidance.

## What it does

- Parses the JSON output from a Veracode local SCA scan
- Reports **SCA findings**: vulnerable dependencies matched to CVEs, with severity, fix versions, and recommended actions (Upgrade / Replace / Remove)
- Reports **IaC findings**: Dockerfile and configuration misconfigurations grouped by severity
- Flags detected **secrets** as critical priority
- Supports both a default **Summary** mode and an on-request **Detail** mode for drilling into a specific component or CVE

## Usage

Install this skill in your VS Code Copilot setup, then ask Copilot about a Veracode scan result file:

> "Summarise this Veracode scan: `/path/to/veracode.json`"

> "Give me details on the `log4j-core` findings in `veracode.json`"

## Repository contents

| Path | Description |
|------|-------------|
| `SKILL.md` | Skill definition and agent instructions |
| `REFERENCE.md` | Full JSON schema reference for Veracode local SCA output |
| `scripts/sca_summary.py` | Extracts a summary table of vulnerable dependencies |
| `scripts/iac_summary.py` | Extracts IaC/Dockerfile findings sorted by severity |
| `scripts/sca_detail.py` | Extracts detailed CVE info for a specific component |

## Scripts

The scripts under `scripts/` can also be run directly against a scan file:

```bash
# Dependency vulnerability summary
python3 scripts/sca_summary.py path/to/veracode.json

# IaC/Dockerfile misconfiguration summary
python3 scripts/iac_summary.py path/to/veracode.json

# Detailed CVE info for a specific component
python3 scripts/sca_detail.py path/to/veracode.json <component-name>
```

**Requirements:** Python 3.6+, no third-party dependencies.

## Remediation priority

1. **Upgrade** — update to the version listed in `fix.versions[]`
2. **Replace** — find an alternative if `fix.state` is `wont-fix` or `not-fixed`
3. **Remove** — remove the dependency if it is unused
