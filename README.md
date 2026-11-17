# Line item billing prototype

A password-gated GitHub Pages share of the Jobber project prototype: Project
Billing → Vision on Backyard reno (its Overview, Work progress and Billing
tabs), for testing line item billing. A Line items payment's invoice bills only
its line items, at the share of each set on the payment schedule. It
opens on the empty payment schedule (FTUX); a new schedule starts on Amount,
so testers pick the structure themselves. With Invoicing options, Milestone
billing and Jobber AI off and the Inline line card. A bar along the top switches
the line item picker between Search (the default) and Two-pane, and Jobber AI
between Off (the default), Popover and Chat drawer; either AI version runs with
Search.

The password changes every day at 07:00 UTC (midnight in Edmonton). A
scheduled workflow re-encrypts the snapshot under the new day's password and
redeploys, so a password only works on the day it was given. If a rotation is
missed, the page closes itself 26 hours after its last deploy.

The prototype is never in this repo in the clear: `snapshot.enc` is
AES-256-GCM encrypted with a key derived from the `SHARE_SECRET` Actions
secret, and the deployed `payload.bin` is encrypted with a PBKDF2 key from the
day's password.

## Today's password

```bash
node scripts/seal.mjs password
```

Reads the secret from the macOS keychain item `line-item-billing-share` and
the day from the live page, so it always matches what's deployed.

## Update the prototype

In `jobber-project-prototype`, on `david/line-item-billing-share` (or its
worktree, `jobber-project-prototype-line-items`):

```bash
npm run build:line-item-billing
```

Then here:

```bash
node scripts/seal.mjs snapshot ../jobber-project-prototype-line-items/dist-line-item-billing/index.html
git commit -am "Update snapshot" && git push
```

The push redeploys with today's password.

## Rotate now, or turn it off

- Rotate now: run the **Rotate password and deploy** workflow from the
  Actions tab.
- Take it down: Settings → Pages → unpublish, or delete the repo.
- GitHub disables scheduled workflows after 60 days with no commits; after
  that the page closes on its own within 26 hours.
