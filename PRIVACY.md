# Privacy

Last updated: 2026-09-18

research-assistant-suite-chatgpt is a public, instructions-only coordination package.
The repository contains routing instructions, contract references, and package metadata.
It does not include a hosted backend, account system, telemetry endpoint, analytics SDK,
credentials, cookies, or an automatic upload service.

When the suite runs inside ChatGPT or Codex, the host product processes the conversation,
files, enabled skills, and enabled tools under the host product's own terms and privacy
controls. The suite may pass a structured research request from its framing phase to the
enabled companion literature skill. If the host performs web search or page fetching, the
selected external site may receive the corresponding request. This package does not add
credentials or bypass access controls.

Prompts and project files can contain research questions, unpublished results, sample
metadata, or experimental constraints. Users should avoid placing secrets or unnecessary
personal information in prompts, source files, or generated reports, and should provide
only material they are authorized to process.

The package is maintained in a public GitHub repository. Public issue reports and pull
requests are visible to GitHub users. For support, see [SUPPORT.md](SUPPORT.md). For legal
terms, see [TERMS.md](TERMS.md).
