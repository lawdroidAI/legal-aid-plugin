# Support

Thank you for using this project.

This document explains where to get help, what information to include, and which issues are outside the scope of project support.

## Where to Get Help

Please use the right channel for your request.

| Type of request | Where to go |
| --------------- | ----------- |
| Bug report | Open a GitHub issue using the bug report template |
| Installation, build, or loading problem | Open a GitHub issue using the installation/build template |
| Compatibility problem | Open a GitHub issue using the compatibility template |
| Feature request | Open a GitHub issue using the feature request template |
| Documentation problem | Open a GitHub issue using the documentation template |
| Usage question | Use GitHub Discussions, if enabled |
| Security vulnerability | Follow `SECURITY.md`; do not open a public issue |

Security vulnerabilities must not be reported publicly. See [`SECURITY.md`](./SECURITY.md) for the private reporting process.

## Before Asking for Help

Before opening an issue or discussion, please:

1. Check the `README.md`.
2. Check the documentation, if available.
3. Search existing issues and discussions.
4. Try the latest stable version of the plugin.
5. Check the changelog or release notes for known changes.
6. Remove secrets, tokens, private keys, personal data, and sensitive logs from anything you share.

## What to Include

When asking for help, include enough information for others to understand and reproduce the problem.

Useful details include:

- Plugin version
- Host application version
- Operating system and version
- Browser, runtime, or package manager version, if relevant
- Installation method
- Configuration details, if relevant
- Steps to reproduce the problem
- Expected behavior
- Actual behavior
- Error messages or logs
- Screenshots or recordings, if useful
- What you already tried

Please keep reports focused. One issue should usually describe one problem.

## Plugin-Specific Details

Because this project is a plugin, support requests are much easier to handle when they include:

- The host application name and version
- Whether the plugin was installed from a marketplace, package manager, release artifact, or local build
- Whether the plugin was recently updated
- Whether the host application was recently updated
- Whether the issue happens with other plugins disabled
- Whether the issue happens with default plugin settings
- Any relevant plugin permissions or configuration

Do not share private workspace data, credentials, access tokens, API keys, or sensitive logs.

## Response Expectations

This is an open-source project maintained by volunteers or limited maintainers.

We try to respond to issues and discussions, but response times may vary. A clear, reproducible report is much more likely to receive a useful response.

Maintainers may close issues that are:

- Missing required information
- Not reproducible
- Duplicates of existing issues
- Outside the project scope
- Better suited for the host application’s support team
- Security reports posted publicly
- Abusive, spammy, or not made in good faith

Closed issues can be reopened if new, relevant information is provided.

## Supported Versions

Support is generally provided for the latest stable release.

Older versions may not receive bug fixes, compatibility fixes, or support unless otherwise stated.

Before reporting a problem, please try upgrading to the latest stable release.

## Bugs

Use the bug report template for reproducible problems.

A good bug report includes:

- Clear reproduction steps
- The exact version where the problem occurs
- What you expected to happen
- What actually happened
- Relevant logs or screenshots

Please do not report multiple unrelated bugs in one issue.

## Installation, Build, or Loading Problems

Use the installation/build issue template for problems such as:

- Dependency installation failure
- Local development setup failure
- Build failure
- Packaging failure
- Plugin fails to load
- Marketplace or package manager installation problem

Please include the command that failed and the full relevant error output.

## Compatibility Problems

Use the compatibility issue template when the plugin works in one environment but not another.

Examples:

- Works on one host application version but not another
- Works on macOS but not Windows
- Works in one browser/runtime but not another
- Stops working after a host application update

Please include both the working and broken environments when possible.

## Feature Requests

Feature requests are welcome, but not every feature will be accepted.

A useful feature request explains:

- The problem you are trying to solve
- Why the current behavior is not enough
- The proposed solution
- Any alternatives you considered
- Whether the change may require new permissions, external services, or access to user data

Requests that increase plugin permissions, collect new data, or add external network calls will be reviewed carefully.

## Documentation Issues

Use the documentation issue template for:

- Missing documentation
- Confusing setup instructions
- Outdated screenshots
- Incorrect examples
- Broken links
- Missing troubleshooting steps
- Unclear plugin permissions or privacy information

Documentation improvements are highly appreciated.

## Security Issues

Do not open a public issue for security vulnerabilities.

Security issues may include:

- Exposed secrets or credentials
- Unsafe handling of tokens or private data
- Unauthorized access to host application data
- Injection vulnerabilities
- Remote code execution
- Privilege escalation
- Unsafe plugin permissions
- Supply-chain or release workflow vulnerabilities

Report security issues using the process in [`SECURITY.md`](./SECURITY.md).

## What Is Not Supported

The project may not be able to provide support for:

- Unsupported plugin versions
- Modified forks
- Custom builds not based on the official release process
- Problems caused by unrelated third-party plugins
- Problems caused by unsupported host application versions
- General programming help unrelated to this project
- Private consulting, custom feature development, or guaranteed response times
- Issues in third-party services or platforms unless they directly affect this project

## Contributing Fixes

Pull requests are welcome for bugs, documentation improvements, and other project improvements.

Before contributing, read [`CONTRIBUTING.md`](./CONTRIBUTING.md).

For larger changes, breaking changes, or changes that affect permissions, privacy, security, or compatibility, please open an issue first to discuss the proposed approach.

## Keeping Yourself Updated

To stay up to date:

- Watch the repository for releases or announcements
- Read the changelog or release notes before upgrading
- Use the latest stable release where possible
- Review documentation after major updates

## Contact

For normal support, use GitHub Issues or GitHub Discussions.

For security-sensitive matters, use the private process described in [`SECURITY.md`](./SECURITY.md).
