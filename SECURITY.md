# Security Policy

Thank you for helping keep the Legal Aid Plugin project and its users safe.

This document explains which versions are supported, how to report security issues, and what to expect from the disclosure process.

## Supported Versions

Security fixes are provided for the latest stable release.

| Version | Supported |
| ------- | --------- |
| Latest stable release | Yes |
| Older releases | No, unless otherwise stated |

If you are using an older version, please upgrade to the latest release before reporting an issue, unless the vulnerability affects the latest release as well.

## Reporting a Vulnerability

Please do **not** report security vulnerabilities through public GitHub issues, discussions, pull requests, or comments.

Instead, report security issues using one of the following methods:

- GitHub Private Vulnerability Reporting, if enabled for this repository
- Email: support@lawdroid.com, Subject: Legal Aid Plugin Bug Report

Please include as much detail as possible, including:

- A description of the vulnerability
- Steps to reproduce the issue
- The affected version or commit
- Any relevant configuration details
- The potential impact
- Whether the issue is already public or known elsewhere
- Suggested fixes, if you have them

Please avoid including real user data, production secrets, or destructive proof-of-concept payloads.

## Response Timeline

We aim to respond to security reports within **5 business days**.

After receiving a report, we will:

1. Confirm receipt of the report.
2. Investigate and validate the issue.
3. Assess severity and impact.
4. Work on a fix, mitigation, or advisory.
5. Coordinate disclosure where appropriate.

Fix timelines depend on severity and complexity, but high-impact vulnerabilities will be prioritized.

## Disclosure Policy

We ask that you follow responsible disclosure practices.

Please do not publicly disclose the vulnerability until we have had a reasonable opportunity to investigate and release a fix. We will work with reporters to coordinate disclosure when appropriate.

If the issue is accepted as a valid vulnerability, we may publish a security advisory after a fix or mitigation is available.

## Scope

Security issues may include, but are not limited to:

- Authentication or authorization bypasses
- Remote code execution
- Privilege escalation
- Insecure handling of secrets or tokens
- Injection vulnerabilities
- Cross-site scripting or cross-site request forgery, where applicable
- Unsafe plugin behavior that could compromise users, data, or host applications
- Supply-chain vulnerabilities in project dependencies or release workflows

The following are generally considered out of scope unless they demonstrate a concrete security impact:

- Vulnerabilities only affecting unsupported versions
- Reports from automated scanners without proof of exploitability
- Denial-of-service issues requiring excessive or unrealistic resource usage
- Social engineering
- Physical attacks
- Issues in third-party services, platforms, or dependencies without a demonstrated impact on this project
- Missing security headers or best-practice recommendations without an exploitable vulnerability

## Plugin-Specific Security Expectations

Because this project is a plugin, security reports are especially valuable when they identify risks involving:

- Unexpected access to host application data
- Excessive or incorrectly declared permissions
- Unsafe handling of user-provided input
- Unsafe communication with external services
- Leakage of configuration, credentials, tokens, logs, or user data
- Behavior that differs from the permissions or privacy expectations described in the documentation

## Secrets and Credentials

If you discover exposed credentials, tokens, private keys, or other secrets in this repository, please report them immediately.

If a secret appears to be valid, we will rotate or revoke it as soon as possible. Please do not attempt to use, test, or validate exposed credentials beyond what is necessary to identify the issue.

## Safe Harbor

We will not pursue legal action against security researchers who make a good-faith effort to follow this policy, avoid privacy violations, avoid data destruction, and avoid disruption to users or services.

Good-faith research means:

- You report vulnerabilities promptly and privately.
- You do not access, modify, or delete data that does not belong to you.
- You do not degrade, interrupt, or attack project infrastructure or users.
- You do not exploit the vulnerability beyond what is necessary to demonstrate impact.
- You give us reasonable time to fix the issue before public disclosure.

## Security Updates

Security fixes may be released as:

- A patched release
- A security advisory
- A dependency update
- A configuration or documentation change
- A mitigation notice

Users are encouraged to stay on the latest stable version.

## Questions

For general support, feature requests, or non-security bugs, please use the normal GitHub issue tracker or support channels.

For anything that could put users, data, credentials, systems, or the plugin host environment at risk, please use the private security reporting process described above.
