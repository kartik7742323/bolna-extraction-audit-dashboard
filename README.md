# Bolna Extraction Audit — Dashboard

An audit of whether the **post-call extractions** on a fleet of Bolna voice agents actually capture what each agent's own script says it is evaluating.

An *extraction* (Bolna calls them "dispositions") is the setting that tells the AI what to pull out of a call after it ends. Each extraction fills one field in the CRM. When the extraction logic does not match the agent's script, the CRM fills up with answers that look confident but are wrong, and the humans downstream act on them.

`dashboard.html` is a single self-contained file. Download it, open it in any browser. Nothing to install.

---

## What the audit covered

Window: **21 September – 6 October 2026**.

| | |
|---|---|
| Agents checked | 712, across 178 sub-accounts |
| Agents that actually placed calls | 108, across 66 accounts |
| Calls placed in the window | 204,951 |
| Calls inspected in detail | 4,826 sampled, 2,481 completed with a transcript |
| Problems found | 2,565, on 1,036 calls |

---

## The three biggest structural problems

**1. The script screens on a score the extraction never records.**
35 agents state a minimum percentage a student must clear to be eligible. Only 5 mention that number anywhere in their extraction logic. On the other 30 agents, covering roughly 151,000 calls, the AI tells the student whether they are eligible and then records a verdict that ignores the rule entirely.

**2. "Qualified" means "replied", not "answered".**
54 agents use a qualification prompt where any reply counts as engagement. The agent's opening line is *"Am I speaking to [name]?"*, so saying "yes" to that alone is enough to become a Qualified lead. 21% of all Qualified verdicts rest on fewer than 15 words from the contact.

**3. Seven agents have no extractions attached at all.**
They placed 12,754 calls in the window. Every one of those conversations was transcribed and then discarded.

---

## The 7 per-call checks

Every inspected call is tested against these rules.

| Check | What it catches | Severity |
|---|---|---|
| Qualified on thin engagement | Lead marked Qualified when the contact said under 15 words | Serious |
| Qualified below the gate | Lead marked Qualified despite scoring below the script's own minimum | Serious |
| Verdict on dead air | A verdict written on a call where the contact said nothing at all | Serious |
| Forced binary | A Yes/No field had to pick an answer because there was no "don't know" option | Warning |
| Low-confidence write | A value written at confidence below 0.40 | Warning |
| Prose in field | A free-text field wrote a sentence instead of leaving itself blank | Warning |
| Short-call verdict | A verdict written on a call under 25 seconds | Warning |

### How much to trust each number

The three **Serious** checks are exact. They test a recorded verdict against a stated number, a zero-word transcript, or a word count. Treat every hit as confirmed.

The two largest **Warning** checks key off word count, so they produce some false positives. One found during review: an agent recorded `cf_ai_callback_request = Yes` on a 9-word call and was flagged — but the contact had said *"can you contact me at another time?"* in Kannada, so Yes was the right answer.

Read those two checks as *"this field had no safe way to say I don't know"* rather than *"this answer is wrong"*. The structural weakness is real in every case. The individual answer sometimes is not.

---

## Using the dashboard

**Overview** — scope, headline numbers, daily call volume, and the accounts needing attention first.

**Clients** — the main view.
1. Click an account name. Its detail opens below.
2. Click an agent name to narrow to just that agent.
3. Below that is the list of flagged calls, each with its execution ID, duration, and how many words the contact said.
4. **Click any call row.** It expands to show what happened, what the contact actually said, why that is wrong, and the exact fix.
5. **Copy execution IDs** copies the whole filtered set.

**Issue types** — works the other way round. Pick a problem type and see every account it affects.

**Findings** — the structural problems that exist in the configuration regardless of any single call.

**Remediation** — the fix order, plus paste-ready text to drop straight into a disposition's question field.

---

## Sampling

Call counts and agent-level flags cover **every** call in the window.

The per-call issue lists come from a sample of up to **100 calls per agent**. Treat them as worked examples, not a complete register.

---

## What is not in this repository

- **API bearer tokens.** Kept entirely outside version control.
- **Agent IDs.** Every internal agent identifier has been replaced with an opaque key. Agents are shown by name only.
- **Raw call transcripts.** Only short illustrative fragments appear, attached to the specific issue they demonstrate.
- **Phone numbers or any contact identifiers.**
- **The audit scripts and raw API pulls.** Held separately.
