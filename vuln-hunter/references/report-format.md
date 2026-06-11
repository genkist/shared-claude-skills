# Report Format & Severity Guidelines

## Severity Definitions

Use these definitions consistently across all phases.

| Severity | CVSS Range | Criteria |
|----------|-----------|---------|
| **Critical** | 9.0–10.0 | Unauthenticated, remotely exploitable, high impact (RCE, auth bypass, mass data exfiltration). Requires immediate fix before deploy. |
| **High** | 7.0–8.9 | Authenticated or requires some precondition; significant impact (privilege escalation, sensitive data exposure, significant data manipulation). |
| **Medium** | 4.0–6.9 | Requires specific conditions or has partial impact. Important to fix but not blocking. |
| **Low** | 0.1–3.9 | Defense-in-depth, very low impact or very unlikely exploitation. |
| **Info** | n/a | Missing best practice with no direct exploitability; noted for awareness. |

## Confidence Levels

Each finding must include a confidence rating. Only report findings with confidence ≥ 40%.

| Confidence | Meaning |
|-----------|---------|
| **High (80–100%)** | The vulnerability is present based on static code analysis alone; no additional assumptions needed |
| **Medium (60–79%)** | The vulnerability is likely present; one plausible assumption about runtime configuration or data flow is needed |
| **Low (40–59%)** | The vulnerability may be present depending on conditions not visible in this code (e.g., external config, calling context) |
| **Discard (<40%)** | Too speculative; do not include in report |

## Finding Format (Full — Critical/High)

```markdown
### [SEVERITY] [Vulnerability Class] — [Short descriptive title]

**Confidence**: High / Medium / Low  
**File**: `path/to/file.ext:LINE_NUMBER`  
**CWE**: CWE-XXX ([name])  
**Reachability**: [Externally reachable via X] / [Internal only] / [Requires auth]

**Description**  
2–4 sentences explaining the flaw: what the vulnerable code does, why it's exploitable, and what an attacker can achieve.

**Why AI-generated code is prone to this**  
1 sentence explaining the LLM pattern that produces this flaw.

**Vulnerable code**  
\`\`\`language
[minimal snippet showing the flaw]
\`\`\`

**Proof of concept**  
\`\`\`
[minimal payload, curl command, or input that demonstrates exploitability]
\`\`\`

**Remediation**  
\`\`\`language
[corrected code snippet]
\`\`\`
Additional guidance if needed (link to docs, library recommendation).
```

## Finding Format (Abbreviated — Medium/Low)

```markdown
### [SEVERITY] [Class] — [Title]
**File**: `path/file.ext:LINE`  
**Description**: [One sentence]  
**Fix**: [One sentence or code snippet]
```

## Infra Finding Format (Full — Critical/High)

```markdown
### [SEVERITY] [Vulnerability Class] — [Short descriptive title]

**Confidence**: High / Medium / Low
**Resource**: `resource_type.resource_name` (`file.tf:LINE`) / `namespace/kind/name` (`manifest.yaml:LINE`) / service `name` (`docker-compose.yml:LINE`)
**CWE**: CWE-XXX ([name])
**Effective**: Yes — [attached to / affects X] / Partially mitigated by [Y] / Theoretical — [not yet provisioned / template only]

**Description**
2–4 sentences explaining the misconfiguration: what the setting does, why it is dangerous, and what an attacker can achieve.

**Blast radius**
What resources, data, or systems are exposed or compromised if this is exploited.

**Attack scenario**
Concise narrative of how an attacker would exploit this (no need for a curl command — describe the steps).

**Vulnerable config**
\`\`\`hcl / yaml / nginx
[minimal snippet showing the misconfiguration]
\`\`\`

**Remediation**
\`\`\`hcl / yaml / nginx
[corrected config snippet]
\`\`\`
Additional guidance if needed (policy reference, CIS Benchmark control).
```

## Infra Finding Format (Abbreviated — Medium/Low)

```markdown
### [SEVERITY] [Class] — [Title]
**Resource**: `resource.name` (`file:LINE`)
**Description**: [One sentence]
**Fix**: [One-line config change or pointer to remediation]
```

## Report Header

```markdown
## Security Scan Report

**Scan target**: [full scan / git diff / file path / directory]
**Language / Framework**: [detected]
**Infrastructure detected**: [Terraform · Kubernetes · Docker · nginx] or "none"
**Scan date**: [date]
**Total findings**: N (Critical: X · High: Y · Medium: Z · Low: W)
  Code findings: N · Infra findings: N

> AI-generation signals detected: [yes/no — describe if yes]
```

## False Positives Section

Always include this section for transparency:

```markdown
## False Positives Discarded

The following were investigated and ruled out:

- **[Class]** at `file:line` — [One sentence explaining why it's not exploitable]
- ...

*Ruling out false positives is as important as finding real issues.*
```

## Coverage Gaps Section

```markdown
## Coverage Gaps

The following areas could not be fully analyzed:

- Minified/transpiled code (only source analyzed)
- External configuration not included in scan
- Third-party libraries (only call sites, not library internals)
- Dynamic code generation at runtime
```

## CVSS Quick Scoring Reference

For High/Critical findings, optionally include a CVSS v3.1 vector:

**Attack Vector (AV)**: N=Network, A=Adjacent, L=Local, P=Physical  
**Attack Complexity (AC)**: L=Low, H=High  
**Privileges Required (PR)**: N=None, L=Low, H=High  
**User Interaction (UI)**: N=None, R=Required  
**Scope (S)**: U=Unchanged, C=Changed  
**Confidentiality (C)**, **Integrity (I)**, **Availability (A)**: N=None, L=Low, H=High  

Example: `CVSS:3.1/AV:N/AC:L/PR:N/UI:N/S:U/C:H/I:H/A:N` = 9.1 Critical

## Remediation Priority Matrix

Use this to prioritize when multiple findings exist:

```
Priority 1 (Fix before deploy):
  Critical + Externally reachable
  High + Externally reachable + High confidence

Priority 2 (Fix in current sprint):
  High + Internal/Auth-required
  Medium + Externally reachable + High confidence

Priority 3 (Next sprint):
  Medium + Internal/Auth-required
  Low + Externally reachable

Priority 4 (Backlog):
  Low + Internal
  Info items
```
