# INIT

Set up SDD for the current GitHub repository.

**Language argument:** `$1` determines the language for Issue templates.
- `ko`, `korean`, `한국어` → Korean
- `ja`, `japanese`, `日本語` → Japanese
- `en`, `english`, or empty → English (default)

1. Copy Issue templates from `${CLAUDE_SKILL_DIR}/templates/{lang}/issue_*.yml` to `.github/ISSUE_TEMPLATE/`
   - Select the template directory based on the language argument
2. Save the selected language to `.github/.sdd-lang` file for other commands to reference
3. Create GitHub labels:
   ```bash
   gh label create "sdd:analyze" --color "1d76db" --description "SDD: Requirements Analysis" --force
   gh label create "sdd:design" --color "0e8a16" --description "SDD: Design" --force
   gh label create "sdd:implement" --color "e4e669" --description "SDD: Implementation" --force
   gh label create "sdd:test" --color "f9d0c4" --description "SDD: Testing" --force
   gh label create "sdd:done" --color "0075ca" --description "SDD: Done" --force
   gh label create "sdd:child" --color "d4c5f9" --description "SDD: Child Issue" --force
   ```
4. Report completion with a summary of what was installed.
