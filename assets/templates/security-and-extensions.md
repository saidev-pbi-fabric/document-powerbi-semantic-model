# Security & Extensions — [Model Name]

Covers model elements that don't fit the core data dictionary/measures/relationships files:
hierarchies, calculation groups, perspectives, and RLS roles. Include only the sections that
apply to this model — omit a section entirely if the model has none of that element, rather
than padding with "none found."

## Hierarchies

| Hierarchy | Table | Levels (top → bottom) | Notes |
|---|---|---|---|
| [name] | [table] | [Level1 > Level2 > ...] | [purpose if evident, e.g. "drill path for date slicers"] |

## Calculation groups

### [Calculation Group Name]

[One sentence: what this calculation group is for, e.g. "switches a measure between YTD, MTD,
and prior-period views without needing a separate measure per period."]

**Calculation items:**

#### [Item Name]

[Purpose — one plain-language sentence, same rule as `measures.md`.]

```dax
[Item Name] = <DAX expression, typically wrapping SELECTEDMEASURE()>
```

*(Repeat per calculation item, per calculation group.)*

## Perspectives

| Perspective | Tables/columns/measures included | Likely audience |
|---|---|---|
| [name] | [summary — not an exhaustive column list unless the perspective is small] | [inferred, e.g. "finance-only subset"] |

## Row-level security (RLS) roles

**Documented for reference only — role membership and access grants are out of scope for this
skill; see the Power BI/Fabric portal for that.**

Before filling in the Filter expression column, check literal string/value comparisons in the
DAX for confidential business terms (region names, deal/customer codenames, restricted org
codes) — RLS filters are the most likely place in a model for a business-sensitive literal to
appear verbatim. Redact/generalize per `privacy-and-redaction.md` before writing the row; don't
rely solely on the final output-wide check.

| Role | Table | Filter expression (DAX) | Effect |
|---|---|---|---|
| [role name] | [table] | `[DAX filter]` | [plain-language: what rows this role can see] |

*(Repeat per table the role filters, if it filters more than one.)*

## Notes

- [Any hierarchy/calculation group/perspective/role that couldn't be fully explained — cross-
  reference `open-questions.md` rather than duplicating detail here.]
