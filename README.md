# Case 03 — Local Account Creation and Activity

## Scenario

The fictitious local account `SOC_LAB` was created and enabled on `SOC-WS01`, then used for an interactive logon.

## Objective

Reconstruct the account lifecycle, identify the creator, verify subsequent authentication, and determine whether the account was added to a privileged local group.

## Data Source

- Windows Security Event Log
- Event IDs `4720`, `4724`, `4738`, `4722`, `4624`, and `4732`
- Splunk index: `windows_soc`

## Detection / Investigation

I began with Event ID `4720` to confirm the account creation and identify the creator. I then followed the activity associated with `SOC_LAB` across the account-management and authentication events. Finally, I ran a separate check for local-group membership changes.

- [`detection.spl`](detection.spl) identifies newly created local accounts.
- [`investigation.spl`](investigation.spl) reconstructs the account activity timeline.
- [`group-membership-check.spl`](group-membership-check.spl) checks specifically for Event ID `4732`.

## Evidence

```text
Host: SOC-WS01
Creator: lab_analyst
New account: SOC_LAB

4720 → account created
4724 → password reset attempt
4738 → account modified
4722 → account enabled
4624 → successful interactive logon (Logon Type 2)
4732 → not observed for SOC_LAB during the reviewed investigation window
```

See [`evidence/README.md`](evidence/README.md) before adding public evidence.


| Investigation step | Observed result |
| --- | --- |
| Host reviewed | `SOC-WS01` |
| Creating account | `lab_analyst` |
| New local account | `SOC_LAB` |
| Account created | Event ID `4720` observed |
| Account enabled | Event ID `4722` observed |
| Password reset attempt | Event ID `4724` observed |
| Account modified | Event ID `4738` observed |
| Successful use | Event ID `4624`, Logon Type `2` observed |
| Local-group addition | Event ID `4732` not observed during the reviewed investigation window |

## Analysis

I confirmed that `lab_analyst` created `SOC_LAB`, after which the account was enabled, modified, and used for a successful interactive logon. I searched the same host and investigation window for Event ID `4732` but found no event linking `SOC_LAB` to a security-enabled local group. This result applies only to the reviewed data and time range; it does not prove that a membership change could never have occurred outside that scope.

## Classification

**True Positive / Benign Activity** — the account-management events occurred as detected and were generated intentionally in the lab.

## Severity

**Low** in the authorized exercise. Unapproved account creation, privileged group membership, unexpected creator identities, or use on sensitive systems would justify higher severity.

## Recommended Action

Document and close the lab case. In production, validate authorization, review the creator's session, inspect group membership and account properties, search for subsequent logons and process activity, and disable the account if unauthorized.


## MITRE ATT&CK

- **T1136.001 — Create Account: Local Account:** directly relevant to the creation of the local account.
- **T1078.003 — Valid Accounts: Local Accounts:** behaviorally relevant because the newly created local account was subsequently used for an interactive logon; no malicious use was identified in this exercise.

## Lessons Learned

- Account investigations require a timeline, not a single event.
- Creation, enabling, modification, and logon events provide complementary context.
- Privileged-group checks are essential after account creation.
- A missing event should be reported as “not observed” within a defined scope.

## Analyst Conclusion

Event ID `4720` confirmed that `lab_analyst` created the local account `SOC_LAB` on `SOC-WS01`. I reconstructed the subsequent activity and verified that the account was enabled, modified, and used for an interactive logon. My separate Event ID `4732` search returned no evidence of a local-group addition within the reviewed scope. Because the actions matched the account lifecycle I intentionally generated in the lab and no unexpected privilege assignment was observed, I classified the case as a true positive with benign context and closed it.
