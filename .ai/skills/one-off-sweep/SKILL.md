---
name: one-off-sweep
description: Brian's pattern for clearing single-use email out of Gmail - sign-in links, 2FA codes, verifications. List, wait for his ok, archive. No unsubscribing. Use when he asks for a one-off sweep.
---

# One-off sweep (Brian's default)

Account: brian.tubergen@gmail.com.

## What it means

Mail that had a purpose for about 60 seconds and is dead weight after: it was never meant to be kept, and there's nothing to unsubscribe from.

- sign-in / magic links ("Sign in to Grok Bot")
- 2FA and one-time codes, OTPs
- email verification / confirm-your-address
- password reset links
- device or new-login approval prompts
- "your code is 123456"

His example: "Sign in to Grok Bot".

## What to leave alone

- security *alerts* about something that happened (new sign-in detected, password changed, suspicious activity) - those are a record, not a one-time token
- account recovery mail tied to something in progress
- anything with a code he might still need: if it arrived in the last hour or two, leave it and say why

## The flow

1. Scope to `in:inbox` unless he says otherwise.
2. List every match: sender + subject + date. Group repeats from the same service with a count instead of listing each.
3. **Wait for his explicit ok before archiving.**
4. On his go: remove INBOX in one batch. No label. Report the count.

Never unsubscribe from anything in this sweep - these are transactional and he wants them to keep arriving.

## Related

marketing-sweep (promotional mail, does unsubscribe), payments-sweep (money mail, labels 4_Payments), junk-filter (recurring sender to auto-file).
