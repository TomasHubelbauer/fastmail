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

## Locked Down Tokens

As of current, it appears that the tokens are full-inbox in scope and I am not
aware of a way to lock them down to a subset of the inbox.
I would welcome this option so that a more focused scope token could be made
available to scripts hosted on 3rd party platforms without the worry of a full
inbox email leak.
I have email jmapsupport@fastmail.com inquiring about this option.
I suggested adding the ability to specify subset of inbox to return in the
responses using folders, patterns on the email data and metadata or generalized 
using saved searches associated with the token during its creation.

Fastmail develops a programming language called Sieve which is used for scripts
that filter and take further actions on emails. Associating a Sieve script with
an API token seems like an ideal solution for this.
https://www.fastmail.help/hc/en-us/articles/1500000280481-Sieve-scripts

Here is a Sieve playground:
https://www.fastmail.com/cgi-bin/sievetest.pl

## Webhooks

I have inquired to Fastmail in the same email mentioned above about the option
of introducing webhook support such that new email meeting predefined criteria
would trigger a webhook call to tie automation to.
