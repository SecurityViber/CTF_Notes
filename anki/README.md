# Anki Cards

`cdsa.txt` holds flashcards for the HTB Academy **SOC Analyst** path / **CDSA** certification,
built from the notes in [Blue Teaming](../Blue%20Teaming/).

The cards are in **English** on purpose: the module text and the exam are English, so recall
happens in the language you are tested in. (The German deck in `WorkingInCybercrime/anki/`
follows the same file format but a German source.)

## Import (Anki 2.1.55 or newer)

1. In Anki: **File → Import** → pick `cdsa.txt`
2. The settings come from the file header:
   - Separator: pipe (`|`), HTML enabled
   - Column 3 = **Deck**: the subdecks `CDSA::SOC`, `CDSA::Incident Handling`,
     `CDSA::Windows Event Logs` are created automatically
   - Column 4 = **Tags**
3. Note type: **Basic**
4. **Import**

## Updating

When new cards are added, import the same file again. Anki matches existing cards on the
first field (the question):

- **Existing notes:** choose «Update», so changed answers are picked up
- Your review progress is kept

> Rewording a **question** makes Anki treat it as a new card. The old one has to be deleted
> by hand.

## Line format

```
Question|Answer (line breaks with <br>)|CDSA::Topic|tag1 tag2
```

- Never use a literal `|` in the text. For PowerShell pipelines write `&#124;`,
  which renders as `|` because the header sets `#html:true`
- Wrap commands in `<code>...</code>`
- Avoid `<` and `>` in text, they are parsed as HTML

## Tags

| Tag | Meaning |
| --- | ------- |
| `cdsa` | every card in this deck |
| `list` | full-list cards, e.g. «name all 12 triage steps» |
| `comparison` | cards that separate two similar concepts |
| `scenario` | practical cases: symptom in, Event ID out |
| `powershell` | query syntax |

Topic tags (`eventlogs`, `eventid`, `logontype`, `killchain`, `nist`, `triage`, …) mirror the
source notes.

**Study tip:** study the parent deck `CDSA`, not only the subdecks. Mixed topics are what
force you to tell similar things apart (4768 vs. 4769, logon type 3 vs. 10).

The `list` cards are the hard ones. If they slow a session down, suspend them with the search
`tag:list` and keep the atomic cards running — the atomic cards teach the same content in
pieces, the list cards only check whether you can reproduce it whole.

## When a note changes

Cards are **updated in place**, never duplicated. The question text is the identity key, so
regenerating a card after a note edit keeps the question byte-identical and only rewrites the
answer, deck or tags. Re-import, pick «Update», and the review progress stays.

This only works if question text never has to change. So counts stay out of the question
wherever a list can still grow:

- «Name the high-value Event IDs of the Security log.» — the table grows as new IDs are learned
- «Name the typical roles in a SOC.» — same

Counts are kept only where the list is canonical and fixed: the 7 Cyber Kill Chain stages,
the 4 NIST phases, the 12 triage steps, the 3 containment steps. Those numbers are part of
what has to be recalled.
