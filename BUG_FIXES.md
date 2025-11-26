# Glide Interview

## Ticket UI-101: Dark Mode Text Visibility

Inside `@app/globals.css`, the background and foreground colors for dark mode were inverted. After correcting these variables, the dark mode theme now effectively mirrors the light mode theme. Currently, this fix effectively disables dark mode support, but allows for it to be properly implemented as a future feature update.

## Ticket VAL-202: Date of Birth Validation

The Date of Birth input field previously lacked age validation. I added a validation check to ensure users are at least 18 years old. If a user fails this validation, the form displays an error message and prevents them from proceeding to the next step. To prevent similar issues in the future, we should always verify the requirements for date inputs and implement appropriate validation constraints.

## Ticket VAL-206: Card Number Validation

Credit card validation requires passing the Luhn algorithm and verifying the card provider. To address this, I integrated the `card-validator` package to handle these checks on the client side. As an additional step, I restricted acceptance to Visa and Mastercard only, assuming these are the currently supported providers. This configuration can be adjusted later as we expand support to other card providers.

## Ticket VAL-208: Weak Password Requirements

To enhance password security, I updated the validation logic to include three new requirements: at least one uppercase letter, one lowercase letter, and one special character. This ensures stricter and more robust password standards.

## Ticket SEC-301: SSN Storage

Previously, SSNs were stored in the database without encryption. To fix this, I introduced a server-side environment variable named `SSN_KEY`. (Note: In my local environment, I have set this to a placeholder value). I implemented a reversible encryption function specifically for SSNs, though it can be adapted for other sensitive data. Moving forward, we should identify other private user information that requires encryption and apply similar protection.

## Ticket SEC-303: XSS Vulnerability

This fix involved removing the `dangerouslySetInnerHTML` attribute from the span element and rendering the content as plain text instead. On the backend, account descriptions are currently generated manually based on account type. However, this fix safeguards against Cross-Site Scripting (XSS) attacks should we allow users to write custom descriptions in the future.

## Ticket PERF-401: Account Creation Error

Previously, if the database operation to create an account succeeded but the subsequent fetch failed, the system would return incorrect default data instead of an error. I updated the logic to throw an appropriate error if the created account cannot be retrieved. The frontend is already equipped to handle account creation errors, so this change was limited to the server. While addressing this, it would be prudent to review other API routes that perform insert or create operations to ensure failed database operations are handled correctly.

## Self Reported Ticket: Successful funding returns oldest transaction

```js
const transaction = await db
    .select()
    .from(transactions)
    .orderBy(transactions.createdAt)
    .limit(1)
    .get();
```

This bug stemmed from the default behavior of Drizzle's `.orderBy` method, which sorts in ascending order. For this use case, we need the most recent transaction (descending order). I fixed this by explicitly specifying the sort direction: `.orderBy(transactions.createdAt, 'desc')`.

## Ticket PERF-405: Missing Transactions

This issue appears to be twofold:

1. The server does not return an error if the funding database operation fails.
2. The frontend transaction history table does not automatically refetch data after a new funding event.

The server-side fix follows the same pattern implemented in PERF-401. On the frontend, after a successful funding event, we refetch the transaction data to show the user his latest transaction without having the user needing to refresh the page.
