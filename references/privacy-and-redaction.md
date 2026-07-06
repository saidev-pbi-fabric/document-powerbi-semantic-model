# Privacy and Redaction — Required Before Any Output

This is the one reference loaded twice in the standard workflow: once before inspecting
source material, once before emitting final output. Both passes matter — a source-level
redaction doesn't guarantee the generated prose is also clean.

## Always redact or generalize

- Tenant IDs, workspace IDs, item/object GUIDs
- Connection strings, server names, database names, SQL endpoints
- File paths containing a username or company-identifying folder structure
- Internal security group names (e.g., `GSG-*`-style naming, distribution list names)
- Sample data rows containing real customer, employee, or production business data
- Proprietary business terms, internal project codenames, or client names — unless the user
  has explicitly confirmed this output is for internal/private use and accepted that scope

## How to redact, not just omit

Prefer generalizing over deleting silently — silent omission can make documentation look
complete when it isn't. Replace a real example with a generic equivalent and say so:

> *Instead of:* `Server=contoso-sql-prod-01.database.windows.net;...`
> *Write:* `Server=<your-sql-server>;...` *(connection details redacted for this example)*

> *Instead of:* a real customer name in a sample data-dictionary row
> *Write:* `"Acme Corp"` or `"Customer A"`, noting the substitution

## Scope confirmation

If the user hasn't stated whether this output is for public/open-source release or private/
internal use, ask before assuming. Default to the stricter (public-safe) standard when
genuinely unclear — it's easier to relax redaction for confirmed-private use than to walk back
an accidental leak.

## Final public safety check

Before presenting any output as finished, re-read the generated files (not the source) and
confirm:

1. No literal tenant/workspace/item GUIDs appear anywhere in the output.
2. No real server names, connection strings, or file paths with usernames appear.
3. No internal group/team names or genuinely private business terminology survived into the
   prose (a redacted source table can still get a real name typed into a summary sentence by
   mistake — check the prose, not just the tables).
4. Any example data used to illustrate a concept is generic/synthetic, not a real row from the
   inspected model, unless the user explicitly confirmed private/internal scope.

## Testing and demonstration

Never use a real private/production model as the test or demo case for this skill itself — use
a public/synthetic sample model. This matters independent of any specific user's request: it
keeps the skill's own examples and test artifacts safe to publish alongside it.
