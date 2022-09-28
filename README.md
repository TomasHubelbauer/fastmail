# Fastmail

Let's take a look at the Fastmail API tokens!

https://www.fastmail.com/developer/integrating-with-fastmail

We can use the examples Fastmail have provided in their JMAP-Samples repository:

https://github.com/fastmail/JMAP-Samples/tree/main/javascript

The samples include a hello-world sample which creates an email draft and a
top-ten sample, which lists the top of the inbox.

The samples use the `fetch` API natively available in the Node API.
Node 18+ must be used to run them.
NPM is not needed.
I opened an issue related to this: https://github.com/fastmail/JMAP-Samples/issues/16

The samples are run as such:

```bash
JMAP_USERNAME=tomas@hubelbauer.net JMAP_TOKEN=fmu1-… node hello-world/top-ten
```

`JMAP_USERNAME` is the email address.

`JMAP_TOKEN` is the token from https://www.fastmail.com/settings/security/tokens

If you omit either of `JMAP_USERNAME` or `JMAP_TOKEN`, the script will notify
you.

- [ ] Ask Fastmail support about folder-scoped API tokens

  It is possible to filter out data in the response, but for automations, it
  would be ideal to get a more locked down scope other than just "all of the
  email" even if the token is readonly. My ideal scenario is that a token could
  be locked down to either a folder, a pattern in the subject/body or a list of
  search results with predetermined criteria.
