# Freyr-TL Repository Base Branches

Update this file when PROD or UAT base branches change. The agent reads this before creating feature branches.

**Repos root:** `/Users/macbook/Documents/Freyr-TL repos/`

| Repository | PROD base branch | UAT base branch |
|---|---|---|
| freyr-fep-backend-core | `prod-revert-16-feb` | `uat-jan16` |
| freyr-fep-web-app | `prod-rollback-16-feb` | `uat-feb17` |
| freyr-fep-backend-notifications | `prod-main` | `uat-jan16` |
| freyr-fep-commons | `prod-copy-24-09-25-9-30-pm` | `uat-jan16` |

## Submodule mapping

| Parent repository | Submodule path | Commons repo |
|---|---|---|
| freyr-fep-backend-core | `services/freyr-fep-commons` | freyr-fep-commons |

Notifications commons submodules are managed separately — do not update them in this skill.

## Notes

- For `freyr-fep-commons`, use the standalone repo at `/Users/macbook/Documents/Freyr-TL repos/freyr-fep-commons/` — not the submodule at `services/freyr-fep-commons/` inside backend-core when creating branches. Submodule sync (Step 6) updates the embedded copy after branches are pushed.
