# Evidence Instructions

Evidence should show the sanitized account lifecycle without exposing real identities.

- Use `SOC-WS01`, creator `lab_analyst`, and account `SOC_LAB`.
- Replace SIDs with `<USER_SID>` and logon identifiers with `<LOGON_ID>`.
- Remove record numbers, personal paths, unrelated accounts, and unnecessary raw-event content.
- Replace secrets with `<REDACTED_SECRET>` or `<REDACTED_SPLUNK_SECRET>` as appropriate.
- If documenting no `4732` result, include the search scope and avoid claiming universal absence.

Suggested files: `account-timeline.png` and `group-membership-check.png`.

