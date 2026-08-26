---
name: junk-filter
description: Brian's standing pattern for quieting a recurring sender in Gmail. Use whenever he asks to junk, filter, mute, or stop seeing a specific kind of email - by sender, subject, or both.
---

# Junk filter (Brian's default)

When Brian asks to filter/junk/mute a recurring email, do this without asking him to spell it out.

## The filter he means

Gmail filter on brian.tubergen@gmail.com:
- Criteria: `from` = the real sender address, and/or `subject` = the real subject string
- Actions: apply label **Junk** (top-level, capital J, `Label_12`) + remove `INBOX`

He has ~27 sub-labels under Junk (Junk/Coned, Junk/Spectrum, ...). He does **not** want new sub-labels created by default - use plain Junk unless he says otherwise.

## Non-negotiables

1. **Verify the real sender and subject first.** He types these from memory and gets them wrong (e.g. "paymenk" for "payment"). Search his mailbox, find the actual messages, and build the filter from the real strings. Report the corrected values back to him.
2. **Gmail cannot route to the Spam/Junk folder via a filter.** The API rejects the SPAM label. Only skip-inbox, label, or delete-it are possible. The Junk label is the substitute; don't offer "delete it" for anything that looks like a record (payments, receipts, statements).
3. **Filters can't be edited in place.** To change one, delete and recreate it, and report the new filter id.
4. **Don't clobber neighboring mail.** Check whether the sender also sends things worth keeping (Verizon's "Your bill is now available online") and scope the subject narrowly enough to spare them.
5. **Check for an existing filter** on that sender before creating a duplicate. He has 165+ filters.

## Backfill

Filters only affect future mail. After creating one, count the existing matches, note how many are actually still in the inbox versus already archived, and offer to label + archive them. Don't do it unasked.

## Reporting back

Tell him: the corrected from/subject, what the filter does, the count already in the inbox, and any deviation from what he asked for. Keep it short.
