---
name: writing-error-messages
description: Write and review error messages, exception text, validation messages, log messages, and API error responses so users can diagnose and fix the problem themselves. Use when authoring new error paths, reviewing error strings in a diff, auditing vague messages like "Invalid input" or "Server error", or designing API/RPC error payloads. Based on Google's "Writing Helpful Error Messages" course.
---

# Writing Helpful Error Messages

Every error message must answer two questions:

1. **What went wrong?**
2. **How does the user fix it?**

A message that answers only the first is incomplete. A message that answers neither
is a bug.

Good error messages let users help themselves and cut the support workload.

## Quick checklist

Apply to each error string you write or review:

- [ ] Does not fail silently — the failure is actually reported
- [ ] States the specific cause, not a generic category
- [ ] Echoes the offending input value back to the user
- [ ] States the requirement/constraint with concrete numbers, names, limits
- [ ] Says how to fix it (action, flag, button, doc link)
- [ ] Includes an example of valid input when the format is non-obvious
- [ ] Concise, but not cryptic
- [ ] No double negatives, no exceptions-to-exceptions
- [ ] Terminology matches the target audience's knowledge
- [ ] Terminology and format consistent with sibling messages
- [ ] Long explanations moved behind a link or progressive disclosure
- [ ] Positive tone: no blame, no apology, no humor
- [ ] (Back end) carries an error code and a stable error identifier

## 1. Explain the problem

### Identify the cause

Be specific. Vague messages frustrate users.

| ❌ | ✅ |
|---|---|
| `Bad directory.` | `The /var/data directory exists but is not writable. To add files to this directory, the directory must be writable. [How to make it writable.]` |
| `Invalid field 'picture'.` | `The 'picture' field can only appear once on the command line; this command line contains the 'picture' field 3 times. Note: prior to version 2.1 you could specify 'picture' more than once; newer versions no longer support this.` |

### Identify the user's invalid input

When the user can enter or edit a value, show **which** value broke and **why**.
Pair the bad value with the expected one so the user can compare at a glance.

| ❌ | ✅ |
|---|---|
| `Funds can only be transferred to an account in the same country.` | `You can only transfer funds to an account within the same country. Sender account's country (UK) does not match the recipient account's country (Canada).` |
| `Invalid postal code.` | `The postal code for the US must consist of either five or nine digits. The specified postal code (4872953) contained seven digits.` |
| `Bid too low.` | `The specified bid ($5) is below the minimum bid ($8).` |

For long or multi-line invalid input: truncate to the essential part, or put the
full value behind a click-to-expand control.

### Specify requirements and constraints

Don't assume users know your system's limits. Quantify them.

| ❌ | ✅ |
|---|---|
| `The combined size of the attachments is too big.` | `The combined size of the attachments (14MB) exceeds the allowed limit (10MB). [Possible solution.]` |
| `Permission denied.` | `Permission denied. Only users in data-eng-oncall have access. [How to join the group.]` |
| `Time-out period exceeded.` | `Time-out period (30s) exceeded. [Possible solution.]` |

## 2. Explain the solution

### Explain how to fix the problem

Make the message **actionable**: cause *and* remedy.

| ❌ | ✅ |
|---|---|
| `The client app on your device is no longer supported.` | `The client app on your device is no longer supported. To update the client app, click the **Update app** button.` |
| `Could not fetch resource: Quota 'CPUS' exceeded. Limit: 1.0 in region us-central-1.` | `This request requires 2.0 CPUs, but the CPUS quota for region us-central-1 is 1.0. Either request a quota increase or deploy to a region with available CPU quota. See <doc link>.` |

When several fixes exist, list them. When the fix takes multiple steps, link to docs
instead of inlining the whole procedure.

### Provide examples

When the required format isn't self-evident, show it.

| ❌ | ✅ |
|---|---|
| `Invalid email address.` | `The email address "ada@" is missing a domain. For example: ada@example.com` |
| `Invalid input.` | `The --config flag requires a YAML file. For example: --config=/home/ada/app.yaml` |
| `Do not declare types in the initialization list.` | Show the wrong line and the corrected line side by side. |

The strongest messages combine all three: the invalid input received, the
requirement, and a valid example.

## 3. Write clearly

### Be concise, not cryptic

Cut filler; prefer active voice. But don't cut so much that meaning goes with it.

| ❌ wordy | ✅ |
|---|---|
| `Unable to establish connection to the SQL database` | `Can't connect to the SQL database` |
| `The resource was not found and cannot be differentiated` | `Resource <name> isn't in cluster <name>` |
| `The SiteID you have entered is invalid` | `Invalid SiteID <SiteID>` |

`Unsupported` is too terse — it names neither the thing nor the reason.

### Avoid double negatives

Two negatives (`not`, `can't`, `won't`, `no`, `prevents`, `forbidding`) force the
reader to untangle the logic. State it affirmatively.

| ❌ | ✅ |
|---|---|
| `You cannot not invoke this flag.` | `You must invoke this flag.` |
| `The universal read permission on <pathname> prevents the operating system from forbidding access.` | `The universal read permission on <pathname> enables anyone to read this file. Giving access to everyone is a security flaw.` |

Also avoid exceptions to exceptions. Replace nested conditions with a plain list of
what's required:

❌ `The App Engine service account must have permissions on the image, except the
Storage Object Viewer role, unless the Storage Object Admin role is available.`

✅ List the required roles directly.

### Write for the target audience

Match the vocabulary to what the reader actually knows; beware the curse of
knowledge.

| Audience | ❌ | ✅ |
|---|---|---|
| Non-ML users | `Exploding gradient problem. Consider gradient clipping.` | Plain-language cause plus a concrete next step. |
| Online shoppers | `Server dropped request; farm at 92% CPU capacity.` | `System overloaded. Your cart is saved. Try again in 5 minutes.` |
| Non-technical users | `Enter a floating-point number.` | `Enter an age that doesn't contain a decimal point. For example: 32` |
| App programmers | `File not open.` | `Call the open_file function first. For example: my_input_stream = open_file('~/.bashrc')` |

Words like *server*, *client*, *farm*, and *CPU* are jargon to a general audience.

### Use terminology consistently

Within a product area, one concept gets one name. Variety helps prose; it hurts
error messages. Keep formats parallel too — the same failure must produce the same
message everywhere in the app.

❌ `Can't connect to cluster at 127.0.0.1:56. Check whether minikube is running.`

✅ `Can't connect to minikube at 127.0.0.1:56. Check whether minikube is running.`

Ignore synonym suggestions from authoring tools here.

### Format for readability

- **Link out** when the explanation runs several sentences:
  ❌ `Post contains unsafe information.`
  ✅ `Post contains unsafe information. Learn more about safety at <link>.`
- **Progressive disclosure**: short message first, expandable detail behind a click.
- **Proximity**: for coding errors, put the message as close as possible to the
  offending line, with caret markers (`^^^`) pointing at the exact token.
- **Don't rely on color alone** — color-blind users will miss it. Pair color with
  boldface, extra whitespace, or text symbols.

### Set the right tone

| Rule | ❌ | ✅ |
|---|---|---|
| Be positive — say what to do | `You didn't enter a name.` | `Enter a name.` |
| | `You entered an invalid postal code.` | `Enter a valid postal code. [What counts as valid.]` |
| Don't over-apologize — drop *sorry*, *please* | `We're sorry, a server error occurred...` | `Google Docs is temporarily unable to open your spreadsheet...` |
| No humor — users are already frustrated, and jokes don't translate | `Is the server running? Better go catch it :D` | `The server is temporarily unavailable. Try again in a few minutes.` |
| Don't assign blame | `You specified a printer that's offline.` | `The specified printer is offline.` |

## 4. Error handling (engineering side)

- **Never fail silently.** Failure is inevitable; failing to report failure is
  inexcusable. A silent failure leaves users unsure whether the operation
  succeeded and leaves support with nothing to diagnose.
- **Follow the language's style guide** for error handling (C++, Java, Python,
  JavaScript, Go).
- **Implement the full error model** for APIs: `google.rpc.Status` with canonical
  codes from `google.rpc.Code`; follow Google AIP / Cloud API design guidance.
- **Don't emit generic errors.** `Server error` masks the root cause. Distinguish
  service failure, network failure, permission failure, quota, and bad input.
- **Log numeric error codes** alongside the text so support can monitor and debug;
  document every code, internal and external.
- **Raise errors as early as possible.** Delayed detection multiplies debugging
  cost and lets failures cascade.


## Rewriting an existing message

1. Name the real cause — replace the category with the specific failure.
2. Interpolate the offending value and the expected value/limit.
3. Append the fix: an action, a flag, a button, or a doc link.
4. Add a valid example if the format is non-obvious.
5. Trim filler, flip to active voice, remove double negatives.
6. Check tone: no blame, no apology, no jokes.
7. Check consistency against neighboring messages in the same product area.
8. Add the error code and identifier if this is a back-end or API error.

## Source

Google's technical writing course, *Writing Helpful Error Messages*:
https://developers.google.com/tech-writing/error-messages — see
`references/source-pages.md` for the full page index.
