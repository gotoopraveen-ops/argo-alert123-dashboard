# Argo Alert — next dashboard

Published at https://gotoopraveen-ops.github.io/argo-alert123-dashboard/

This is the proving ground. The one at `argo-alert-dashboard` is the build that
gives demos, and nothing moves into it until it has worked here.

## What this one has that the other does not

**A sign-in screen.** Every customer gets a login. No password is stored in this
page: each record is the broker credential encrypted with that customer's own
password, so reading the source gives a salt and a blob. Signing in also fixes
the topic prefix, so a customer only ever reaches their own panels.

**Per panel keys.** The broker credential is shared by the whole fleet, so it
never said which panel anyone belonged to. A phone earns a panel's key by being
on that panel's own Wi-Fi, and passes it to this page in the link. A browser
holding keys lists only the panels it holds keys for.

**Signed commands.** The key is never published. What goes on the wire is an
HMAC over the command text and a counter, so reading the command topic teaches
an eavesdropper nothing reusable and a captured command cannot be replayed.

## What it needs

| | |
|---|---|
| Firmware | 4.0.0, remote API level 5 |
| App | 4.0.0 |

It will not control firmware older than 3.2.0. Those panels accept only
unsigned commands, which this page no longer sends. That incompatibility is the
whole reason the two dashboards exist.

## Adding a customer

From the project root, one command, which writes this page and the app together
so they cannot drift apart:

```bash
python3 make_customer.py stmarys "their-password" stmarys
```

The third argument is the customer's topic prefix on the broker. Commit and push
both repositories afterwards, and rebuild the APK.

Existing login for testing: `demo` / `demo1234`, tenant `demo`.

## Still open

Anyone holding the broker credential can read a panel's state, because the panel
has already published it. This page no longer shows it to them, but no page can
un-publish it. Closing that needs one broker credential per customer with
permissions on their own topic prefix, which is console work in HiveMQ rather
than code.
