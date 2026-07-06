# Data Dictionary — [Model Name]

One section per table. Mark inferred descriptions clearly — don't present a guess as
confirmed metadata.

## [Table Name]

**Role:** Fact / Dimension &nbsp;·&nbsp; **Grain:** [one row per ...] &nbsp;·&nbsp;
**Storage:** [Import / DirectQuery / Direct Lake]

| Column | Type | Hidden? | Display Folder | Description |
|---|---|---|---|---|
| [ColumnName] | [dataType] | Yes/No | [folder or —] | [description — mark "(inferred)" if not from source metadata] |

*(Repeat the table block for every table in the model.)*

## Notes on this dictionary

- [Any columns/tables that couldn't be fully documented and why — cross-reference
  `open-questions.md` rather than duplicating detail here.]
- [Any naming-consistency observations worth a reader's attention.]
