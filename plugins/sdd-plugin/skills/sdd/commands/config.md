# CONFIG

**SDD Configuration Management**

Manage SDD settings stored in `.github/.sdd-config`.

## Command Parsing:

Parse the arguments from `$1` onwards:
- No arguments → **Show current config**
- `--skip-review=<values>` → **Set skip-review**
- `--skip-review=` (empty value) → **Reset skip-review**

## Show Current Config:

1. Read `.github/.sdd-config`
   - If file doesn't exist → report "No config set. Using defaults (all reviews enabled)."
2. Display current settings in a readable format:
   ```
   SDD Config (.github/.sdd-config)
   ─────────────────────────────────
   skip-review: analyze, design, implement
   ```
3. Show a summary of what each skipped review means:
   | Value | Skipped Review |
   |-------|---------------|
   | `analyze` | User review after analysis |
   | `design` | User review after design |
   | `implement` | User review at TDD substeps (3-0 ~ 3-3) |
   | `pr` | User review at PR code review (3-4) |
   | `qa` | Manual QA execution (4-2 ~ 4-3) |

## Set skip-review:

1. Validate the values — allowed values: `analyze`, `design`, `implement`, `pr`, `qa`
   - If any invalid value is found → report error with the invalid value and list allowed values
2. Write to `.github/.sdd-config`:
   ```
   skip-review: <comma-separated values>
   ```
3. Confirm the setting was saved and show what reviews will be skipped

## Reset skip-review:

1. Remove the `skip-review` line from `.github/.sdd-config` (or delete the file if it's the only setting)
2. Confirm: "skip-review reset. All reviews are now enabled."
