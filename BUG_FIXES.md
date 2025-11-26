# Glide Interview

- What caused each bug
- How the fix resolves it
- What preventive measures can avoid similar issues

**Ticket UI-101: Dark Mode Text Visibility**

Inside @app/globals.css, the background and foreground colors when in dark mode were flipped. After flipping these variables, the dark mode just acts like light mode. At this moment, this fix will make it where we don't support dark mode, which can be a future feature update.

**Ticket VAL-202: Date of Birth Validation**

Date of birth input field had no validation for age. Added a validation check that makes sure users are 18 years old. If the user fails the validation, the form will display that error to the user and prevent them from moving to the next step. To prevent these types of bugs in the future, double check the reason for date inputs and write appropriate validation based on the constraints.

**Ticket VAL-206: Card Number Validation**

When validating credit card numbers, it needs to pass the Luhn algorithm, and only authorize which provider you want. For the fix, I added a package, card-validator, that does this on the browser side. As an extra validation step, I also added a check for Visa and Mastercards only, assuming that is the only cards we support at the moment. This can change afterwards depending on which card providers we want to / are able to support.

**Ticket VAL-208: Weak Password Requirements**

To enhance our password validation, I added 3 new rules to the existing password validation, adding the requirement of at least 1 capital letter, 1 lowercase letter, and 1 special character. This should make the password validation more robust.

**Ticket SEC-301: SSN Storage**

We are not encrypting the SSN when the server adds to the database. To fix this, we need to add an environment to our server only. The environment variable should be named SSN_KEY. At the moment in my local env file, I have it saved as "thisissofun". For the encryption, I made a function that we can rework for other values we would want to encrypt later, but for now its specifically named for the SSN. The encryption is reversable, if we ever need to pull the users SSN. While we are working on this ticket, we should also consider any other private user information we would like to encrypt, and encrypt those values as well.

**Ticket SEC-303: XSS Vulnerability**

This was a small fix, I remove the dangerouslySetInnerHTML tag from the span, and just rendered text. On the backend, we are creating the descriptions manualy with the account type, so this fix will prevent scripting attacks in the event where in the future, we allow users to write their own descriptions.

**Ticket PERF-401: Account Creation Error**

After the DB operation to make a new entry in the accounts table, we try to fetch the account that we made. But we were not throwing an error if the account fetch failed. We were just giving the user back incorrect default data. The fix is that now, after attempting to fetch the created account, if we can't get it, we throw an error with an appropriate message. The front end is already setup to handle errors in account creation, so we just needed to add it to the server. Since we are alreay in this ticket, to prevent other similar bugs, this would be a good opportunity to go over other api routes that do inserts or creates and double check if we handle failed DB operations correctly.

**Self Reported Ticket, Successfull funding returns oldest transaction**

```js
const transaction = await db
    .select()
    .from(transactions)
    .orderBy(transactions.createdAt)
    .limit(1)
    .get();
```

This bug is caused by the default functionality of the .orderBy in drizzle. By default, it orders by ascending order. When in this case, we would need the descending case. To fix it, just added a new param to the orderBy function like this `.orderBy(transactions.createdAt, 'desc')`

**Ticket PERF-405: Missing Transactions**

From what I can see, this issue has 2 parts. 1 part is the server does not return an error if the funding DB operation failed. The second issue is that on the frontend, if the user is looking at the transaction history, and we make a new funding event, the table should refetch just like how the balance refetches. The first server fix would be a simialr solution that I did on PERF-401.
