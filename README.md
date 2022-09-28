# Fastmail

Let's take a look at the Fastmail API tokens!

https://www.fastmail.com/developer/integrating-with-fastmail

We can use the examples Fastmail have provided in their JMAP-Samples repository:

https://github.com/fastmail/JMAP-Samples/tree/main/javascript

This is the hello world sample:

```
#!/usr/bin/env node
// bail if we don't have our ENV set:
if (!process.env.JMAP_USERNAME || !process.env.JMAP_TOKEN) {
  console.log("Please set your JMAP_USERNAME and JMAP_TOKEN");
  console.log("JMAP_USERNAME=username JMAP_TOKEN=token node hello-world.js");

  process.exit(1);
}

const hostname = process.env.JMAP_HOSTNAME || "api.fastmail.com";
const username = process.env.JMAP_USERNAME;

const authUrl = `https://${hostname}/.well-known/jmap`;
const headers = {
  "Content-Type": "application/json",
  Authorization: `Bearer ${process.env.JMAP_TOKEN}`,
};

const getSession = async () => {
  const response = await fetch(authUrl, {
    method: "GET",
    headers,
  });
  return response.json();
};

const mailboxQuery = async (apiUrl, accountId) => {
  const response = await fetch(apiUrl, {
    method: "POST",
    headers,
    body: JSON.stringify({
      using: ["urn:ietf:params:jmap:core", "urn:ietf:params:jmap:mail"],
      methodCalls: [
        ["Mailbox/query", { accountId, filter: { name: "Drafts" } }, "a"],
      ],
    }),
  });
  const data = await response.json();

  return await data["methodResponses"][0][1].ids[0];
};

const identityQuery = async (apiUrl, accountId) => {
  const response = await fetch(apiUrl, {
    method: "POST",
    headers,
    body: JSON.stringify({
      using: [
        "urn:ietf:params:jmap:core",
        "urn:ietf:params:jmap:mail",
        "urn:ietf:params:jmap:submission",
      ],
      methodCalls: [["Identity/get", { accountId, ids: null }, "a"]],
    }),
  });
  const data = await response.json();

  return await data["methodResponses"][0][1].list.filter(
    (identity) => identity.email === username
  )[0].id;
};

const draftResponse = async (apiUrl, accountId, draftId, identityId) => {
  const messageBody =
    "Hi! \n\n" +
    "This email may not look like much, but I sent it with JMAP, a protocol \n" +
    "designed to make it easier to manage email, contacts, calendars, and more of \n" +
    "your digital life in general. \n\n" +
    "Pretty cool, right? \n\n" +
    "-- \n" +
    "This email sent from my next-generation email system at Fastmail. \n";

  const draftObject = {
    from: [{ email: username }],
    to: [{ email: username }],
    subject: "Hello, world!",
    keywords: { $draft: true },
    mailboxIds: { [draftId]: true },
    bodyValues: { body: { value: messageBody, charset: "utf-8" } },
    textBody: [{ partId: "body", type: "text/plain" }],
  };

  const response = await fetch(apiUrl, {
    method: "POST",
    headers,
    body: JSON.stringify({
      using: [
        "urn:ietf:params:jmap:core",
        "urn:ietf:params:jmap:mail",
        "urn:ietf:params:jmap:submission",
      ],
      methodCalls: [
        ["Email/set", { accountId, create: { draft: draftObject } }, "a"],
        [
          "EmailSubmission/set",
          {
            accountId,
            onSuccessDestroyEmail: ["#sendIt"],
            create: { sendIt: { emailId: "#draft", identityId } },
          },
          "b",
        ],
      ],
    }),
  });

  const data = await response.json();
  console.log(JSON.stringify(data, null, 2));
};

const run = async () => {
  const session = await getSession();
  const apiUrl = session.apiUrl;
  const accountId = session.primaryAccounts["urn:ietf:params:jmap:mail"];
  const draftId = await mailboxQuery(apiUrl, accountId);
  const identityId = await identityQuery(apiUrl, accountId);
  draftResponse(apiUrl, accountId, draftId, identityId);
};

run();
```

- [ ] Test out the hello world sample and see if it works fine

  They don't list `node-fetch` in their dependencies but it is in the lockfile.
  This might be an error that I can send a PR to fix.

- [ ] Ask Fastmail support about folder-scoped API tokens

  It is possible to filter out data in the response, but for automations, it
  would be ideal to get a more locked down scope other than just "all of the
  email" even if the token is readonly. My ideal scenario is that a token could
  be locked down to either a folder, a pattern in the subject/body or a list of
  search results with predetermined criteria.
