# ContractIQ

An AI-powered legal contract tool (drafting, redlining, extraction, obligation tracking)
built day-by-day while working through the 16-Day Claude Code Curriculum.

- Curriculum source: `ContractIQ_16Day_Curriculum.md` (lives alongside this repo, gitignored - reference material, not part of the build)
- Day-by-day learn/build/commit plan: see [PLAN.md](./PLAN.md)
- Status: Day 0 - repo scaffolded, not yet started

## Layout

```
./
├── PRD.md                     # Day 1
├── CLAUDE.md                  # Day 2+ (project brain, extraction schema, rules)
├── .claude/
│   ├── settings.json          # Day 2 (MCP servers, permissions, hooks registration)
│   ├── commands/               # Day 5, 13 (slash commands)
│   ├── hooks/                  # Day 7 (formatting/validation/audit hooks)
│   └── agents/                 # Day 9 (contract-extractor sub-agent)
├── drafting/                   # Day 4-5
├── redlining/                  # Day 6-7
├── extraction/                 # Day 8-9
├── obligations/                # Day 14-15
├── contracts/                  # Day 12 (CI trigger dir - real docs gitignored)
├── .github/workflows/          # Day 12
└── run_pipeline.sh             # Day 16
```

Real/confidential contract text is never committed - see `.gitignore`. Only synthetic
or public (e.g. SEC EDGAR) sample documents are tracked.
