# ContractIQ - 16-Day Learn + Build + Commit Plan

Repo: git@github.com:sumanthkeshav/contractiq.git
Local path: this directory (contractiq)

## Git workflow

**One branch (`main`), linear history, one tag per day.** No feature branches / PRs -
the curriculum itself is sequential (each day's build depends on the previous one), and
Day 12's CI workflow triggers on *push*, not on PRs, so a branch-per-day model would
fight the material instead of reinforcing it.

- Commit message convention: `Day N: <what shipped>` (e.g. `Day 4: NDA drafting engine + template`)
- Tag convention: `dayNN` (e.g. `day04`) applied right after that day's commit
- Push cadence: push after every day's commit, minimum. Push more often if you're
  switching machines or just want the safety net - it costs nothing.
- Never commit: real/confidential contracts, API keys, tokens, `.env` files -
  `.gitignore` already blocks the obvious paths (`contracts/`, `extraction/input/`,
  `extraction/output/*.json`, `*.token`, `.env*`). Only synthetic or public
  (SEC EDGAR) sample contracts get tracked.
- Before every commit: `git status` first, then `git diff --staged` - re-check nothing
  sensitive slipped in, especially from Day 10 onward once MCP tokens enter `.claude/settings.json`.

### Daily ritual

```
1. Read the day's docs (LEARN column below)
2. Do the BUILD exercise
3. git status                                 # see what changed
4. git add <specific files>                   # never `git add -A` blindly
5. git commit -m "Day N: <summary>"
6. git tag dayNN
7. git push origin main --tags
```

If a day's exercise naturally splits into sub-steps (e.g. Day 7 has three separate
hooks), it's fine to make 2-3 small commits through the day and let the `dayNN` tag
land on the last one - just keep every commit self-contained and buildable.

---

## Day-by-day

| Day | Phase | Learn | Build (deliverable) | Commit message | Tag |
|---|---|---|---|---|---|
| 1 | Foundation | Overview, How Claude Code Works, Quickstart, Platforms, Terminal Config | Install Claude Code; `git init` this repo (done); write `PRD.md` (vision, 6 use cases, in/out of scope) | `Day 1: PRD and project init` | `day01` |
| 2 | Foundation | Memory (CLAUDE.md), .claude directory, Settings reference, Env vars, Authentication | `CLAUDE.md` with 8 sections incl. the verbatim no-fabrication/citation/null rule; `.claude/settings.json` (model, allowedTools, maxTokens) | `Day 2: CLAUDE.md project brain + settings.json` | `day02` |
| 3 | Foundation | Context Window Explorer, Best Practices, Costs, Model Config, Fast Mode, Checkpointing | `contracts/chunker.sh` (splits PDF into 5-page overlapping chunks); written decision guide: compact vs clear vs chunking vs model switch | `Day 3: context chunker + compaction guide` | `day03` |
| 4 | Drafting | Common Workflows, Interactive Mode, Plan Mode, Tools Reference, Output Styles | `drafting/templates/nda_template.md` (12 clauses); `drafting/draft_nda.sh`; 3 sample outputs in `drafting/samples/` | `Day 4: NDA drafting engine + template` | `day04` |
| 5 | Drafting | Commands Reference, Skills, Features Overview, Keybindings | `.claude/commands/draft-nda.md`, `draft-msa.md`, `draft-sow.md` - interactive slash commands, validated inputs, saved to `drafting/output/` | `Day 5: /draft-nda /draft-msa /draft-sow slash commands` | `day05` |
| 6 | Redlining | Common Workflows, Code Review pattern, Checkpointing, Output Styles, Plan Mode | `redlining/playbooks/msa_playbook.md` + `nda_playbook.md`; `redlining/redline.sh`; sample `redline_report.md` | `Day 6: playbook-based redline engine` | `day06` |
| 7 | Redlining | Hooks Guide, Hooks Reference, Security (hooks), Sandboxing, Settings Reference | 3 hooks: `post_redline.sh`, `validate_input.sh`, `freshness_check.sh`; wired in `.claude/settings.json` hooks[]; `hooks_test_log.txt` | `Day 7: redline automation hooks (format, validate, freshness)` | `day07` |
| 8 | Extraction | CLI Reference (headless), Headless/Programmatic Use, CLAUDE.md, Costs (Haiku) | Expand `CLAUDE.md` to full 22-field extraction schema; `extraction/extract.sh`; 3 validated JSONs in `extraction/output/` | `Day 8: 22-field extraction schema + extract.sh` | `day08` |
| 9 | Extraction | Sub-Agents, Agent Teams, Costs, Checkpointing | `.claude/agents/contract-extractor.md` (Haiku, extraction-only); `extraction/parallel_extract.sh`; `extraction/merge_portfolio.sh`; `master_portfolio.json` (5+ contracts) | `Day 9: parallel sub-agent extraction at scale` | `day09` |
| 10 | MCP | MCP Overview, Platforms, .claude directory (mcpServers), Channels, Channels Reference | Configure Google Drive + Slack MCP in `.claude/settings.json` (tokens via env vars, never literal); verify both with a real round-trip test | `Day 10: Google Drive + Slack MCP connected` | `day10` |
| 11 | MCP | MCP (writes), Permissions, Checkpointing, Security | Notion "Contract Intelligence" DB (22 properties); `extraction/insert_hotspots.sh`; renewal watchlist view; DB URL in README | `Day 11: Notion hotspot DB + auto-insert` | `day11` |
| 12 | MCP | GitHub Actions, GitHub Enterprise Server, Headless in CI, Scheduled Tasks, Web Scheduled Tasks, Code Review | `.github/workflows/contract_review.yml` (push-triggered extract+redline+Slack+issue); `.github/workflows/daily_renewals.yml` (cron digest) | `Day 12: CI pipeline - auto review on push + daily renewals cron` | `day12` |
| 13 | Skills & Plugins | Skills, Plugins, Plugins Reference, Discover Plugins, Plugin Marketplaces | `.claude/skills/contractiq/SKILL.md`; 6 packaged commands; `contractiq.plugin` manifest; `INSTALL.md` verified by a second person | `Day 13: package ContractIQ as an installable plugin` | `day13` |
| 14 | Skills & Plugins | Scheduled Tasks, Web Scheduled Tasks, Slack, Remote Control, Channels | `obligations/check_deadlines.sh` (tiered Slack digest: URGENT/ACTION NEEDED/WATCH); `obligations/morning_digest.sh`; scheduled Mon 8am + daily 8:30am | `Day 14: obligation tracker + scheduled deadline alerts` | `day14` |
| 15 | Hardening | Security, Permissions, Zero Data Retention, Network Config, Server-Managed Settings, Monitoring/OTel, Legal & Compliance, Data Usage | `security_audit.md`; `.claude/hooks/audit_log.sh` (logs every tool access to `obligations/audit/access_log.jsonl`); hardened `allowedPaths`/`denyPaths`; `security_policy.md` | `Day 15: security hardening, audit logging, access policy` | `day15` |
| 16 | Capstone | Best Practices, Analytics, Troubleshooting, Changelog, official courses | `run_pipeline.sh` (detect -> chunk -> extract -> redline -> Notion insert -> obligation check); `pipeline_report_*.md` for 3 contract types; demo recording; full `README.md` | `Day 16: ContractIQ v1.0 - end-to-end pipeline shipped` | `day16` / `v1.0.0` |

---

## Notes

- **Secrets discipline starts mattering at Day 10.** Once MCP servers (Drive, Slack,
  Notion) enter `.claude/settings.json`, reference credentials by env var name, never
  paste tokens literally - `git diff --staged` before every commit from here on.
- **Day 16 tag doubles as a release**: after tagging `day16`, also tag `v1.0.0` and
  consider a GitHub Release with the Loom demo link and `pipeline_report` samples attached.
- This plan assumes solo, sequential progress. If you fall behind or skip around,
  keep the day numbering in commit messages anyway (e.g. `Day 6 (late): ...`) so the
  history stays legible against this plan.
