# Awesome Coding Agent Security [![Awesome](https://awesome.re/badge.svg)](https://awesome.re)

<a href="https://github.com/nukoe-labs/awesome-coding-agent-security#readme"><img src="media/social-preview.png" width="640" alt="How a trapped repository hijacks a coding agent: instruction files, autorun configuration and MCP servers lead to stolen tokens, then to the next repository."></a>

> Attacks, advisories and defenses for AI coding agents (Claude Code, Cursor, GitHub Copilot, OpenAI Codex, Gemini CLI, Windsurf, Cline, Kiro) and the Model Context Protocol (MCP) servers they run.

Opening a cloned repository with a coding agent can now be enough to run an attacker's code. This list keeps the primary sources in one place: incident reports from the teams that found them, vendor advisories, and the settings that stop each attack.

## Contents

- [Why This List](#why-this-list)
- [Incidents and Campaigns](#incidents-and-campaigns)
- [Advisories by Tool](#advisories-by-tool)
  - [Claude Code](#claude-code)
  - [Cursor](#cursor)
  - [GitHub Copilot](#github-copilot)
  - [Gemini CLI](#gemini-cli)
  - [OpenAI Codex](#openai-codex)
  - [Windsurf, Cline, Roo Code and Kiro](#windsurf-cline-roo-code-and-kiro)
  - [MCP Servers and Clients](#mcp-servers-and-clients)
  - [Agent Frameworks](#agent-frameworks)
- [Attack Vectors](#attack-vectors)
- [Defenses and Settings](#defenses-and-settings)
- [Tools](#tools)
  - [Repository and Account Scanners](#repository-and-account-scanners)
  - [MCP and Skill Scanners](#mcp-and-skill-scanners)
  - [Sandboxes](#sandboxes)
  - [CI and Supply Chain](#ci-and-supply-chain)
- [Research and Talks](#research-and-talks)
- [If You Opened a Trapped Repo](#if-you-opened-a-trapped-repo)

## Why This List

General LLM security, jailbreaks and model safety are covered well elsewhere (see the related lists at the end). This list covers only the tools that read your repository, run shell commands and hold your tokens.

Every entry links to a primary source: the vendor advisory, the National Vulnerability Database (NVD) record, the original research or the official documentation. CVE numbers are checked on NVD before they are added, and each attack family sits next to the setting, sandbox or tool that blocks it.

## Incidents and Campaigns

- [Bitdefender: AI Agents Targeted Through Malicious GitHub Repositories](https://www.bitdefender.com/en-us/blog/labs/how-ai-agents-become-new-targets-in-github-malware-campaign) - November 2026. 22 trapped repositories hide orders in `AGENTS.md` and `CLAUDE.md` and a hook in `.claude/settings.json`, then steal CI, cloud and package-registry tokens.
- [Miasma-family worm in testmetest npm packages](https://safedep.io/testmetest-npm-worm-miasma-variant/) - SafeDep, October 2026. Worm that steals credentials, commits poisoned agent configuration into victims' repositories and wipes the home folder when the stolen token is revoked.
- [Shai-Hulud descendant in a RubyGem](https://socket.dev/blog/shai-hulud-descendant-malicious-ruby-gem) - Socket, November 2026. The same worm family spreads through RubyGems and poisons AI tool configuration.
- [Mini Shai-Hulud targets AI coding agents](https://www.sonarsource.com/blog/mini-shai-hulud-targets-ai-coding-agents/) - Sonar, May 2026. First npm worm to persist through a Claude Code `SessionStart` hook and a VS Code `folderOpen` task committed to the victim's branches.
- [Mini Shai-Hulud strikes again](https://safedep.io/mini-shai-hulud-strikes-again-314-npm-packages-compromised/) - SafeDep, 2026. Second wave, 317 npm packages compromised.
- [Clone This Repo and I Own Your Machine](https://0din.ai/blog/clone-this-repo-and-i-own-your-machine) - Mozilla 0DIN, June 25, 2026. A repository with no visible malicious file makes Claude Code open a reverse shell through a payload fetched at run time.
- [Shai-Hulud npm worm](https://www.cisa.gov/news-events/alerts/2025/09/23/widespread-supply-chain-compromise-impacting-npm-ecosystem) - CISA alert, September 23, 2025. Self-replicating worm that compromised over 500 npm packages and published stolen secrets.
- [s1ngularity: malicious Nx packages](https://github.com/nrwl/nx/security/advisories/GHSA-cxm3-wv7p-598c) - Nx advisory, August 26, 2025. Malicious `nx` versions ran local Claude Code, Gemini CLI and Amazon Q with permission checks disabled to search the disk for secrets.
- [s1ngularity impact analysis](https://www.wiz.io/blog/s1ngularity-supply-chain-attack) - Wiz, August 2025. Thousands of secrets leaked to public `s1ngularity-repository` repositories created with the victims' own `gh` token.
- [Amazon Q Developer extension 1.84.0](https://aws.amazon.com/security/security-bulletins/AWS-2025-015/) - AWS bulletin AWS-2025-015, July 2025. A prompt telling the agent to wipe local files and AWS resources was injected into the published VS Code extension.
- [Rules File Backdoor](https://www.pillar.security/blog/new-vulnerability-in-github-copilot-and-cursor-how-hackers-can-weaponize-code-agents) - Pillar Security, March 2025. Invisible Unicode in Cursor and Copilot rules files makes the agent write backdoored code that reviewers cannot see.

## Advisories by Tool

Fixed versions come from the vendor advisory. Update before opening untrusted code.

### Claude Code

- [All Claude Code advisories](https://github.com/anthropics/claude-code/security/advisories) - Vendor list, 36 advisories as of November 2026. The highest fixed version they require is 2.1.260 for the CLI.
- [CVE-2025-59536](https://github.com/anthropics/claude-code/security/advisories/GHSA-4fgq-fpq9-mr3g) - Commands from project files ran before the trust dialog. Fixed in 1.0.111.
- [CVE-2026-33068](https://github.com/anthropics/claude-code/security/advisories/GHSA-mmgp-wc2j-qcv7) - A settings file in the repository bypassed the trust dialog. Fixed in 2.1.53.
- [CVE-2026-40068](https://github.com/anthropics/claude-code/security/advisories/GHSA-q5hj-mxqh-vv77) - Trust dialog bypass through a spoofed Git worktree. Fixed in 2.1.84.
- [CVE-2026-21852](https://github.com/anthropics/claude-code/security/advisories/GHSA-jh7p-qr78-84p7) - Repository environment configuration used before trust confirmation leaked data. Fixed in 2.0.65.
- [CVE-2025-59041](https://github.com/anthropics/claude-code/security/advisories/GHSA-j4h9-wv2m-wrf7) - Code execution through a malicious `git config user.email`. Fixed in 1.0.105.
- [CVE-2025-66032](https://github.com/anthropics/claude-code/security/advisories/GHSA-xq4m-mc3c-vvg3) - General command validation bypass leading to code execution. Fixed in 1.0.93.
- [CVE-2025-64755](https://github.com/anthropics/claude-code/security/advisories/GHSA-7mv8-j34q-vp7q) - A `sed` command passed validation and wrote arbitrary files. Fixed in 2.0.31.
- [CVE-2026-25725](https://github.com/anthropics/claude-code/security/advisories/GHSA-ff64-7w26-62rf) - Sandbox escape by injecting persistent configuration into `settings.json`. Fixed in 2.1.2.
- [CVE-2026-55607](https://github.com/anthropics/claude-code/security/advisories/GHSA-7835-87q9-rgvv) - Unsandboxed code execution through Git worktree path confusion. Fixed in 2.1.163.
- [CVE-2026-88968](https://github.com/anthropics/claude-code/security/advisories/GHSA-5xjx-9qx6-jcrv) - Race in the Edit tool writes files outside the workspace. Fixed in 2.1.251.
- [CVE-2026-54316](https://github.com/anthropics/claude-code/security/advisories/GHSA-fg94-h982-f3mm) - Data exfiltration through the pre-approved Hugging Face domain in WebFetch. Fixed in 2.1.163.
- [CVE-2026-86063](https://github.com/anthropics/claude-code/security/advisories/GHSA-pq7j-f95f-qfcv) - Plugin commit SHA pin bypass installs attacker code. Fixed in 2.1.179.
- [Deny rules skipped after 50 subcommands](https://adversa.ai/blog/claude-code-security-bypass-deny-rules-disabled/) - Adversa AI, 2026. A long compound command silently disabled `deny` rules; no CVE.

### Cursor

- [All Cursor advisories](https://github.com/cursor/cursor/security/advisories) - Vendor list.
- [CVE-2025-54135 (CurXecute)](https://nvd.nist.gov/vuln/detail/CVE-2025-54135) - Prompt injection writes a new MCP configuration file without approval, which runs commands. Fixed in 1.3.9.
- [CVE-2025-54136 (MCPoison)](https://research.checkpoint.com/2025/cursor-vulnerability-mcpoison/) - Check Point, August 2025. An approved MCP configuration is swapped for a malicious one without a new prompt.
- [CVE-2026-22708](https://nvd.nist.gov/vuln/detail/CVE-2026-22708) - Allowlisted shell commands in Auto-Run mode deliver a payload through a poisoned environment. Fixed in 2.3.
- [CVE-2026-50548](https://nvd.nist.gov/vuln/detail/CVE-2026-50548) - Sandbox escape by overwriting the sandbox helper from the writable working directory. Fixed in 3.0.

### GitHub Copilot

- [CVE-2025-53773](https://embracethered.com/blog/posts/2025/github-copilot-remote-code-execution-via-prompt-injection/) - Embrace The Red, August 2025. Injected text makes Copilot set `chat.tools.autoApprove` in VS Code settings, then run commands.
- [CamoLeak](https://www.legitsecurity.com/blog/camoleak-critical-github-copilot-vulnerability-leaks-private-source-code) - Legit Security, 2025. Hidden text in a pull request makes Copilot Chat leak private code through GitHub's Camo image proxy.
- [CVE-2026-29783](https://nvd.nist.gov/vuln/detail/CVE-2026-29783) - Copilot CLI runs commands hidden in bash parameter expansion without approval. Affects 0.0.422 and earlier.

### Gemini CLI

- [All Gemini CLI advisories](https://github.com/google-gemini/gemini-cli/security/advisories) - Vendor list.
- [CVE-2026-12537](https://github.com/advisories/GHSA-wpqr-6v78-jr5g) - A public issue drives the `run-gemini-cli` triage action into running commands and stealing CI secrets. Fixed in 0.39.1.
- [Code execution through deception](https://tracebit.com/blog/code-exec-deception-gemini-ai-cli-hijack) - Tracebit, July 2025. An allowlisted command prefix hides a second command from the user. Fixed in 0.1.14.
- [CVE-2026-0755](https://nvd.nist.gov/vuln/detail/CVE-2026-0755) - Command injection in the third-party `gemini-mcp-tool` package.

### OpenAI Codex

- [All Codex advisories](https://github.com/openai/codex/security/advisories) - Vendor list.
- [CVE-2025-61260](https://research.checkpoint.com/2025/openai-codex-cli-command-injection-vulnerability/) - Check Point, 2025. Codex CLI starts the MCP commands of a repository's `.codex/config.toml` without asking.

### Windsurf, Cline, Roo Code and Kiro

- [CVE-2026-30615](https://nvd.nist.gov/vuln/detail/CVE-2026-30615) - Windsurf. Attacker HTML registers a malicious STDIO MCP server with no click.
- [SpAIware in Windsurf](https://embracethered.com/blog/posts/2025/windsurf-spaiware-exploit-persistent-prompt-injection/) - Embrace The Red, 2025. Prompt injection stored in the agent's memory exfiltrates data in every later session.
- [CVE-2026-44211](https://nvd.nist.gov/vuln/detail/CVE-2026-44211) - Cline. Cross-origin WebSocket hijack. Affects 2.13.0 and earlier.
- [CVE-2026-59723](https://nvd.nist.gov/vuln/detail/CVE-2026-59723) - Cline. The Cline Hub dashboard server is reachable cross-origin. Fixed in 3.0.30.
- [CVE-2025-53098](https://nvd.nist.gov/vuln/detail/CVE-2025-53098) - Roo Code. The agent writes `.roo/mcp.json`, which then runs commands.
- [AWS-2025-019](https://aws.amazon.com/security/security-bulletins/AWS-2025-019/) - Prompt injection issues fixed in Amazon Q Developer and Kiro.

### MCP Servers and Clients

- [CVE-2025-6514](https://jfrog.com/blog/2025-6514-critical-mcp-remote-rce-vulnerability/) - JFrog, 2025. `mcp-remote` runs an OS command from a malicious authorization endpoint. Fixed in 0.1.16.
- [CVE-2025-49596](https://www.oligo.security/blog/critical-rce-vulnerability-in-anthropic-mcp-inspector-cve-2025-49596) - Oligo, 2025. MCP Inspector had no authentication between client and proxy, so a web page could run code. Fixed in 0.14.1.
- [CVE-2025-53109 and CVE-2025-53110](https://cymulate.com/blog/cve-2025-53109-53110-escaperoute-anthropic/) - Cymulate, 2025. Symlink and path-prefix escapes in the reference Filesystem MCP server.
- [CVE-2025-68143](https://nvd.nist.gov/vuln/detail/CVE-2025-68143) - Path traversal in the reference Git MCP server, with CVE-2025-68144 and CVE-2025-68145.
- [CVE-2025-54994](https://nvd.nist.gov/vuln/detail/CVE-2025-54994) - Command injection in the `create-mcp-server-stdio` starter kit.
- [CVE-2026-13341](https://nvd.nist.gov/vuln/detail/CVE-2026-13341) - Indirect prompt injection in the Kong Konnect MCP server triggers unwanted API calls.
- [CVE-2026-33032](https://nvd.nist.gov/vuln/detail/CVE-2026-33032) - The nginx-ui MCP endpoints run commands without proper authentication.
- [All reference server advisories](https://github.com/modelcontextprotocol/servers/security/advisories) - Vendor list.

### Agent Frameworks

- [Prompts become shells](https://www.microsoft.com/en-us/security/blog/2026/05/07/prompts-become-shells-rce-vulnerabilities-ai-agent-frameworks/) - Microsoft, May 2026. Prompt injection to code execution in Semantic Kernel (CVE-2026-26030, CVE-2026-25592).

## Attack Vectors

- [Poisoned instruction files](https://agents.md/) - `AGENTS.md`, `CLAUDE.md`, `GEMINI.md` and `.cursorrules` are read as if the user wrote them, so a cloned repository can give orders.
- [Invisible Unicode](https://embracethered.com/blog/posts/2024/hiding-and-finding-text-with-unicode-tags/) - Tag and zero-width characters hide instructions that the model reads and the human does not.
- [Autorun hooks](https://research.checkpoint.com/2026/rce-and-api-token-exfiltration-through-claude-code-project-files-cve-2025-59536/) - A hook in `.claude/settings.json` is run by the tool itself, with no model decision, once the folder is trusted.
- [Folder-open tasks](https://code.visualstudio.com/docs/debugtest/tasks) - A `.vscode/tasks.json` task with `"runOn": "folderOpen"` runs in VS Code, Cursor and Windsurf when the folder opens.
- [Project MCP configuration](https://nvd.nist.gov/vuln/detail/CVE-2025-61260) - `.mcp.json`, `.cursor/mcp.json` or `.codex/config.toml` starts a local command presented as an MCP server.
- [Allowlist bypass](https://nvd.nist.gov/vuln/detail/CVE-2025-54795) - An approved command such as `echo`, `sed`, `find` or `rg` carries a second command through shell syntax.
- [Tool poisoning and rug pulls](https://invariantlabs.ai/blog/mcp-security-notification-tool-poisoning-attacks) - Invariant Labs, 2025. An MCP tool description hides instructions, or changes after the user approved it.
- [Toxic agent flows](https://invariantlabs.ai/blog/mcp-github-vulnerability) - Invariant Labs, 2025. A public GitHub issue makes an agent with the GitHub MCP server copy private repository data into a public pull request.
- [Exfiltration through allowed channels](https://simonwillison.net/2025/Jun/16/the-lethal-trifecta/) - Private data, untrusted content and any outbound channel together are enough to leak secrets.
- [Agents launched by malware](https://socket.dev/blog/nx-packages-compromised) - Socket, August 2025. A malicious package runs the local agent with `--dangerously-skip-permissions`, `--yolo` or `--trust-all-tools` to do the search for it.
- [Agents in CI](https://nvd.nist.gov/vuln/detail/CVE-2026-12537) - A triage agent that reads public issues and holds CI secrets can be steered by any GitHub user.

## Defenses and Settings

- [Claude Code security](https://code.claude.com/docs/en/security) - Trust dialog, permission model and prompt injection safeguards.
- [Claude Code permissions](https://code.claude.com/docs/en/permissions) - `allow`, `ask` and `deny` rules, and managed settings that users cannot override.
- [Claude Code sandboxing](https://code.claude.com/docs/en/sandboxing) - Operating-system filesystem and network isolation for the Bash tool; `deny` rules alone are not a security boundary.
- [Claude Code hooks](https://code.claude.com/docs/en/hooks) - `PreToolUse` hooks can block a command before it runs; review every hook a repository declares.
- [Claude Code development containers](https://code.claude.com/docs/en/devcontainer) - Reference container with a default-deny firewall for untrusted code.
- [Making Claude Code safer with sandboxing](https://www.anthropic.com/engineering/claude-code-sandboxing) - Anthropic engineering, 2025. Why filesystem and network isolation must both be on.
- [VS Code Workspace Trust](https://code.visualstudio.com/docs/editing/workspaces/workspace-trust) - Restricted mode blocks tasks, debugging and workspace settings until you trust the folder.
- [Secure development with AI in VS Code](https://code.visualstudio.com/docs/copilot/security) - Tool approval, auto-approve settings and MCP server trust for Copilot agent mode.
- [Copilot coding agent firewall](https://docs.github.com/en/copilot/how-tos/use-copilot-agents/coding-agent/customize-the-agent-firewall) - Limit the outbound network access of the cloud agent.
- [Cursor agent security](https://docs.cursor.com/en/agent/security) - Auto-run, allowlists, MCP approval and the terminal sandbox.
- [Codex security](https://developers.openai.com/codex/security) - Sandbox modes, approval policies and network access for Codex CLI and cloud.
- [Gemini CLI sandboxing](https://geminicli.com/docs/cli/sandbox/) - Run tools in a container or macOS Seatbelt profile.
- [MCP security best practices](https://modelcontextprotocol.io/specification/draft/basic/security_best_practices) - Official guidance on confused deputy, token passthrough, session hijacking and local server compromise.
- [GitHub Actions security hardening](https://docs.github.com/en/actions/security-for-github-actions/security-guides/security-hardening-for-github-actions) - Least-privilege tokens, pinned actions and untrusted input for workflows that run agents.
- [npm trusted publishing](https://docs.npmjs.com/trusted-publishers) - Publish from CI with OIDC instead of long-lived tokens that worms steal and reuse.

## Tools

### Repository and Account Scanners

- [medusa](https://github.com/Pantheon-Security/medusa) - Scanner that checks `.claude/` hooks, permissions and editor AI files, including bidirectional Unicode.
- [agentshield](https://github.com/affaan-m/agentshield) - Audits agent configurations, MCP servers and tool permissions.
- [shai-hulud-detect](https://github.com/Cobenian/shai-hulud-detect) - Checks a local project's lockfiles and packages for known Shai-Hulud versions.
- [Shai-Hulud 2.0 Detector](https://github.com/gensecaihq/Shai-Hulud-2.0-Detector) - GitHub Action that scans npm dependencies for the Shai-Hulud 2.0 and ChainDrop campaigns.
- [wormsign](https://github.com/nukoe-labs/wormsign) - Scans every branch of a GitHub account or organization through the API, without cloning, for autorun agent files, hidden payloads and backdated commits. New, by the maintainers of this list.

### MCP and Skill Scanners

- [agent-scan](https://github.com/snyk/agent-scan) - Scans installed MCP servers and agent skills for tool poisoning and pins tool descriptions to detect rug pulls.
- [mcp-scanner](https://github.com/cisco-ai-defense/mcp-scanner) - Scans MCP servers for threats with rules and LLM analysis.
- [skill-scanner](https://github.com/cisco-ai-defense/skill-scanner) - Scans agent skills before installation.
- [SkillSpector](https://github.com/NVIDIA/SkillSpector) - Detects prompt injection and malicious patterns in agent skills.
- [AI-Infra-Guard](https://github.com/Tencent/AI-Infra-Guard) - Red-teaming platform with MCP, skill and agent scans.

### Sandboxes

- [sandbox-runtime](https://github.com/anthropics/sandbox-runtime) - Filesystem and network restrictions for any process, without a container.
- [claude-code-devcontainer](https://github.com/trailofbits/claude-code-devcontainer) - Isolated development container for running Claude Code on untrusted code.
- [container-use](https://github.com/dagger/container-use) - Gives each coding agent its own container and Git branch.
- [stillsuit](https://github.com/nukoe-labs/stillsuit) - Runs Claude Code, Codex or any command in a container whose only exit is an allowlist proxy, with API keys injected by the proxy and every blocked connection named with its process. New, by the maintainers of this list.

### CI and Supply Chain

- [zizmor](https://github.com/zizmorcore/zizmor) - Static analysis of GitHub Actions workflows: injection, unpinned actions, excessive permissions.
- [harden-runner](https://github.com/step-security/harden-runner) - Monitors and blocks network egress from GitHub Actions runners.
- [GuardDog](https://github.com/DataDog/guarddog) - Detects malicious PyPI and npm packages before installation.
- [vet](https://github.com/safedep/vet) - Checks open source dependencies against malicious package data.

## Research and Talks

- [IDEsaster](https://maccarita.com/posts/idesaster/) - Ari Marzouk, December 2025. Over 30 issues in every AI IDE tested, built from normal IDE features such as workspace settings and remote JSON schemas.
- [Agentic ProbLLMs](https://media.ccc.de/v/39c3-agentic-probllms-exploiting-ai-computer-use-and-coding-agents) - Johann Rehberger, 39C3 talk. Exploiting computer-use and coding agents end to end.
- [The Month of AI Bugs](https://embracethered.com/blog/posts/2025/announcement-the-month-of-ai-bugs/) - Embrace The Red, August 2025. One disclosed agent vulnerability per day.
- [Pwn2Own Berlin 2026, day one](https://www.zerodayinitiative.com/blog/2026/5/13/pwn2own-berlin-2026-day-one-results) - First contest with a coding agent category; OpenAI Codex exploited, Claude Code collisions with known bugs.
- [Pwn2Own Berlin 2026, day two](https://www.zerodayinitiative.com/blog/2026/5/15/pwn2own-berlin-2026-day-two-results) - Cursor fully exploited.
- [Pwn2Own Berlin 2026, day three](https://www.thezdi.com/blog/2026/5/16/pwn2own-berlin-2026-day-three-results-and-master-of-pwn) - OpenAI Codex exploited again; more Claude Code collisions.
- [Context Privilege Escalation Attacks against AI Agent Harness](https://arxiv.org/abs/2609.01222) - arXiv, 2026. How content in the agent's context gains privileges it should not have.
- [When Attacker-Controlled Hook Updates Steer AI Agent Harnesses](https://arxiv.org/abs/2609.03884) - arXiv, 2026. Abuse of hook updates to steer agents.
- [How User Invocation Shapes Coding Agent Vulnerability to Repository Poisoning](https://arxiv.org/abs/2608.30686) - arXiv, 2026. How the way an agent is started changes the success of poisoned repositories.
- [MOSAIC](https://arxiv.org/abs/2607.02857) - arXiv, 2026. Composing harmless-looking CLI commands into an attack on coding agents.
- [Isolation, Access Control and TOCTOU Vulnerabilities in AI Coding Agents](https://arxiv.org/abs/2607.05743) - arXiv, 2026. Survey of execution security research for coding agents.
- [OWASP Top 10 for Agentic Applications](https://genai.owasp.org/resource/owasp-top-10-for-agentic-applications-for-2026/) - OWASP GenAI Security Project, 2026 edition.
- [Simon Willison on prompt injection](https://simonwillison.net/tags/prompt-injection/) - Long-running notes on prompt injection and agent design.
- [Mindgard AI IDE vulnerability patterns](https://github.com/Mindgard/ai-ide-vuln-patterns) - Catalog of recurring vulnerability patterns across AI IDEs.

## If You Opened a Trapped Repo

Some worms install a wiper that deletes your home folder as soon as the stolen token stops working. Revoking first can destroy your data. The [full guide](guides/opened-a-trapped-repo.md) gives the safe order:

1. Disconnect the machine from the network.
2. Back up your data to an external drive, without configuration folders such as `~/.config` and `~/.claude`.
3. Look for traces: `~/.claude/package/`, `~/.codex/package/`, unknown hooks in `~/.claude/settings.json`, unknown user services.
4. Reinstall the machine if any trace exists.
5. From another, clean machine, revoke every token and key the infected machine could read.
6. Check your repositories for commits you did not make and new public repositories you did not create.

## Related Lists

- [Awesome MCP Security](https://github.com/Puliczek/awesome-mcp-security#readme) - MCP security resources.
- [Awesome LLM Security](https://github.com/corca-ai/awesome-llm-security#readme) - Tools, documents and projects about LLM security.
- [Awesome AI Security](https://github.com/ottosulin/awesome-ai-security#readme) - AI security resources in general.
- [Awesome AI Security Tools](https://github.com/scadastrangelove/awesome-ai-security-tools#readme) - Tools for AI security and AI-assisted security.
- [Awesome Prompt Injection](https://github.com/Joe-B-Security/awesome-prompt-injection#readme) - Prompt injection resources.
- [Awesome Claude Code](https://github.com/hesreallyhim/awesome-claude-code#readme) - Claude Code resources in general.

## Contributing

Contributions are welcome. Read the [contribution guidelines](contributing.md) first: every entry needs a primary source and a one-line description.
