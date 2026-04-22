# Changelog

## 0.1.0 — Initial release

- 4-layer strategy model (Mission / Philosophy / Goals / Specs) + Issues log
- 5 slash commands: `/init`, `/philosophy`, `/goal`, `/spec`, `/issue`
- PASS/WEAK/FAIL rubrics on strategic layers (B-, P-, G-, S-); structural-only on Issues
- Directory-as-status lifecycle for Goals, Specs, Issues
- Spec→Issue one-way canonical linking via `related_issues:` frontmatter
- `CLAUDE.md` rule block injected into consumer repos to enforce strategic-context reads before code changes
