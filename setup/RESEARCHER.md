# Security Review Scope and Testing Rules

Last updated: September 16, 2026

## Purpose and Policy Precedence

This is a reusable audit template for Web3 smart contracts and blockchain
protocols, Web2 applications and services (including GitLab), and browsers
and native components (including Chromium). It is not an official vulnerability
disclosure or bug bounty policy for any target and does not grant permission
to test deployed infrastructure or live blockchain contracts.

Use it with `RESEARCHER.md` when the user requests a security review. It does
not override the user's task or the assistant's higher-priority instructions.
The target's actual security policy, applicable program scope, and authorized
testing boundaries determine eligibility and permitted activities. Record
which policy and version or access date were consulted. If scope is unclear,
continue source review and isolated local analysis; clarify permission before
conducting testing that depends on it.

Keep the upstream `SECURITY.md` available. When installing these templates in
another repository, retain this guide under a separate name or directory if
that repository already has a security policy; do not replace that policy.

## Scope Record

At the start of a review, record:

- Repository and revision; components included and excluded.
- Target type: Web3 contract/protocol, Web2 application/service,
  browser/native component, or mixed.
- Supported OS, architecture, version, build flags, and feature configuration.
- For Web3: chain, fork block, contract addresses, compiler version, proxy
  implementation, and relevant protocol configuration.
- Protected assets and user, tenant, origin, process, or protocol boundaries.
- Allowed actor capabilities and required victim interaction.
- Authorized local environments, test accounts, and test methods.
- Upstream disclosure policy and program-specific exclusions.

Source access alone is not authorization to probe a running service. Testing
permission and bounty eligibility are separate from whether code has a defect.

## Security Impacts to Evaluate

### Web3: Smart Contracts and Blockchain Protocols

- Theft or unauthorized transfer, minting, burning, or spending of assets.
- Incorrect accounting, insolvency, or loss of collateral and protocol funds.
- Permanent or material temporary freezing of funds or blocked withdrawals.
- Unauthorized state changes, role escalation, initialization, or upgrades.
- Oracle, liquidation, or market manipulation enabled by a code or design flaw.
- Signature, replay, proof, bridge, or cross-chain message validation failures.
- Consensus/state integrity violations or inconsistent state acceptance.
- Reachable denial of service affecting contract operations or protocol nodes.

Distinguish a demonstrated defect from ordinary market losses or assumed
external failures. Lack of liquidity, depegging, incorrect oracle data,
centralization, Sybil behavior, and majority-control attacks do not by
themselves prove a target vulnerability. Investigate whether the target enables
the condition or violates a promised boundary under in-scope assumptions.
Oracle manipulation and flash-loan scenarios are not blanket exclusions;
validate their feasibility and impact against the applicable program policy.

### Web Applications and Services

- Authentication bypass or unauthorized privilege gain.
- Cross-user, cross-project, or cross-tenant access to protected data or actions.
- Sensitive data or credential disclosure through a demonstrated code path.
- Injection, SSRF, unsafe deserialization, or arbitrary file access with a
  concrete security impact.
- Script execution or request forgery violating an application boundary.
- CI/CD, runner, artifact, or scoped-token trust boundary violations.
- Integrity loss, races, or reproducible service disruption from realistic input.

### Browsers and Native Components

- Reachable memory safety violations: out-of-bounds access, use-after-free,
  type confusion, and related defects.
- Origin or site isolation violations and unauthorized cross-origin data access.
- Sandbox escapes, IPC validation failures, or unauthorized privileged actions.
- JavaScript/Wasm engine defects with demonstrated security consequences.
- Parser, network, extension, permission, or filesystem boundary violations.
- Reproducible crashes or resource exhaustion, assessed against the target's
  policy and the affected process and recovery behavior.

Browser defects are relevant when the browser is the audit target. For a web
application review, separate an application defect from a dependency on an
independent browser defect and assess each against its own scope.

## Actor Assumptions and Severity

Start with the least privilege needed to reach the surface, then state the
actual prerequisites. Do not universally exclude authenticated users,
extensions, local users, or compromised renderers: their relevance depends
on the boundary being reviewed and the target's threat model.

For Web3, the default actor has no admin/owner/governance/operator keys,
leaked credentials, or privileged network access. A finding requiring those
capabilities must be explicitly supported by the review scope. Distinguish
permissionless acquisition of a role from assuming possession of its keys.

An action already permitted to an administrator is not a privilege escalation.
A privileged actor crossing a separate enforced boundary can be relevant when
that boundary is in scope. Do not assume stolen credentials or disabled
protections unless those assumptions are explicitly part of the review.

Base severity on demonstrated impact, reachability, privileges, interaction,
configuration, and reliability. Distinguish a crash from code execution, a
renderer defect from sandbox escape, and a test-build observation from
supported-release exposure. Mark uncertain severity as provisional.

## Evidence Filters and Conditional Exclusions

These do not establish a vulnerability on their own:

- Analogy to another project's finding or a scanner's unverified output.
- Missing headers, cookie flags, best practices, or dependency version alerts.
- Stack traces, identifiers, or enumeration without sensitive disclosure or
  another demonstrated security consequence.
- Self-XSS, intended permissions, or user actions without a crossed boundary.
- Hypothetical impact without a supported target-specific execution path.
- Resource exhaustion that assumes unlimited distributed traffic.

Assess these cases according to evidence and the actual target policy. Do not
silently discard security defects just because they are not bounty-eligible.
Tests, build files, and configuration can be relevant if they affect deployed
behavior, artifact integrity, or a supported security boundary.

A secret-like string is not proof of an active credential. Record and redact
suspected exposure; do not authenticate to third-party systems to validate it
without explicit authorization.

## Testing Boundaries

- Use local checkouts, isolated instances, controlled browser builds, synthetic
  data, and accounts owned or explicitly authorized for the review.
- For Web3, use local chains, isolated nodes, or local forks. Forking a public
  network does not authorize broadcasting transactions to it. Keep test
  transactions, oracle manipulation, and third-party contract interactions
  within the isolated environment; do not use real funds or live signing keys.
- Keep reproducers minimal and resource-bounded. Run crash, exhaustion, and
  fuzzing tests in disposable environments with time and resource limits.
- Test live or shared systems only within explicit authorization and applicable
  program rules. Stop if testing causes unexpected external effects.
- Do not access other users' data, exfiltrate secrets, damage data, establish
  persistence, or send attack traffic to unrelated services.
- Use local substitutes for SSO providers, webhooks, and other integrations
  unless testing the real service is expressly authorized.
- Do not conduct phishing or social engineering as part of this workflow.
- Protect sensitive evidence and follow the target's disclosure process;
  drafting a report does not authorize submitting or publishing it.

## Deliverables

Report scope and limitations even when there are no findings. Separate
confirmed issues from hypotheses and hardening suggestions. Include relevant
code locations, validation evidence, impact, and concrete remediation. Prefer
focused local regression tests over operational exploit tooling.
