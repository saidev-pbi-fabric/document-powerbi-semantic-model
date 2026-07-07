## Open Questions

- **No description metadata anywhere in the model** — every column/table description in
  `data-dictionary.md` is inferred from naming, not sourced from actual metadata. Resolve by
  asking the model owner to confirm, or add descriptions in the model itself.
- **Direct Lake source freshness** — `Sales` reads from `dbo.Sales` via Direct Lake; this
  documentation was produced from static TMDL files, not a live connection, so it can't confirm
  the current state of the underlying lakehouse table. If freshness matters, verify against the
  live source rather than relying on this pass.
- **`Regional Manager` role scope not independently verified** — this documents the *defined*
  filter (`[RegionName] = "North"`) from `roles.tmdl`; actual role membership (who is assigned
  to it) is out of scope for this skill and wasn't checked.
