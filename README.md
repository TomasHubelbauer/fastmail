# Fastmail

Let's take a look at the Fastmail API tokens!

https://www.fastmail.com/developer/integrating-with-fastmail

We can use the examples Fastmail have provided in their JMAP-Samples repository:

https://github.com/fastmail/JMAP-Samples/tree/main/javascript

The samples include a `hello-world` sample which creates an email draft and a
`top-ten` sample, which lists the top of the inbox.

The samples use the `fetch` API natively available in the Node 18+. Run like so:

```bash
JMAP_USERNAME=tomas@hubelbauer.net JMAP_TOKEN=fmu1-… node hello-world/top-ten
```

`JMAP_USERNAME` is the email address.

`JMAP_TOKEN` is the token from https://www.fastmail.com/settings/security/tokens

If you omit either of `JMAP_USERNAME` or `JMAP_TOKEN`, the script will notify
you.

## Locked Down Tokens

As of current, I don't see a way to limit a token to a subset of the inbox.
This is a crucial feature in order to avoid leaking my inbox on token leak.
That's a concern when using the token on a 3rd party infrastructure, like CI/CD.

I emailed jmapsupport@fastmail.com and suggested several options of introducing
locked down tokens including associating tokens with folders, patterns (would
operate on email data and metadata), aliases etc.
Later on I found out about Sieve, a filtering and actioning programming language
supported by Fastmail for automated email management.
I have updated my suggestion to say that associating a piece of Sieve code to a
token seems like a preferrable solution.

Information about Sieve:
https://www.fastmail.help/hc/en-us/articles/1500000280481-Sieve-scripts

Sieve playground:
https://www.fastmail.com/cgi-bin/sievetest.pl

Fastmail support told me nothing like this is on the roadmap yet and most of my
suggestions aren't likely to be on the roadmap ever, but they did suggest I
could create a new user and share a folder with them, then I could create a PAT
for that user only and see only the emails they have in the shared folder.

- [ ] Buy a new Fastmail user and share a folder with them and verify this

## Webhooks

In the same conversation where I asked Fastmail about locked down tokens, I have
also inquired about the possibility of adding webhooks which fire when new email
is received.
This would introduce on option of having automations react to new email instead
of poll for new email.
Similarly to the locked down tokens, associating Sieve code with the webhook
seems like the best way to specify what email it should apply to.

Fastmail support told me they might add something akin to webhooks to the Rules
feature on the site. Sieve even used to support something like that, but it was
removed.

Until something like this is added, I will probably stick with polling the user.
