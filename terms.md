# Terms of Use

By using the Chargeprice API, you are accepting the entirety of the following
terms. These terms are subject to change at any time without notice.

## Only use your personal API key

Your API key should be kept secret and only be used within your organisation.

It is not permitted to use any other API key than the one(s) provided by
Chargeprice. It is not allowed to use the API key from
https://www.chargeprice.app or other Chargeprice applications.

Additionally the provided API key must only be used for the application that was
agreed on with Chargeprice. Any use in a new application must be requested
first.

Using a non authorized API or misusing your personal API will result in a report
to legal authorities. 

## Rate Limit

Per default any issued API key is allowed to perform 1000 API requests per minute, 
measured over a time window of 5 minutes.

If your use case requires a higher rate limit, please get in touch with us!

## Reselling data

The API consumer will use the data provided by Chargeprice solely for the agreed
application and is not allowed to share any information or data transferred by
Chargeprice or via the Chargeprice platform without prior written consent from
Chargeprice.

Using a non authorized API or misusing your personal API key will result in a
report to legal authorities. 

This clause has been added after intended data abuse. 

## Caching

It is not permitted to cache any data received from the API for longer than 24
hours on the client-side (e.g. mobile devices). Server-side caching is
prohibited at all. Exceptions are vehicles data in general and data that are
considered as settings, e.g. Storing the selected vehicle or ones selected
tariffs in a local storage. It is explicitly prohibited to cache any price or
charging station related data. Further exceptions need to be explicitly granted
by Chargeprice.

## Avoid unnecessary calls

The API consumer should limit the number of API requests to the minimum to
fulfill the use case.

## Breaking Changes in the API

Chargeprice is constantly adding new features to it's platform and hence APIs
will change. However we are trying our best to avoid changes, which would
potentially break the connection to existing API consumers ("breaking changes").
However also the API consumer is responsible to implement the API in a robust
way. The following (likely not complete) list will hence illustrate what is a
breaking change and what not. API consumers are responsible to deal with
non-breaking changes!

**Breaking Changes**

- The type of an attribute changes (e.g. String to Integer, Integer to `null`)
- The validation of a request parameter is getting more restrictive (e.g. An
  Integer that had no boundaries, is changed to be within 0 and 100)
- A new required request parameter is added
- An attribute is removed from the response
- The structure of the response is changing

**Non-Breaking Changes**

- A new optional request parameter (query, header, body...) is added
- A new attribute is added to the response
- A new enum value is added. Example: Valid Plugs before: `ccs`, `type`. A new
  value gets added: `chademo`.
- The name of a Tariff is changing
- The number of items in an array is changing

## API Lifecycle and Sunsetting

Some new features might not be compatible with the current API and would require
a breaking change though. To avoid this **a new API version**  will be
introduced.

The old and new API will then run in parallel for a period of **at least 90
days**. During this period all API consumers **must** switch to the new API.
Chargeprice will inform all API consumers at the beginning of the period.

At the end of this period the **old API will get deactivated**!

**Hence all API consumers (especially if the API is used in clients that are
hard to upgrade, like Mobile Apps) are responsible to consider the deactivation
of every API from the beginning on!**

## Liability

Chargeprice is not liable for any loss or damage or any expenses incurred by the
API consumer, irrespective of the legal grounds on which any such claim may be
based (breach of obligation, contract, tort etc.).

Chargeprice will do all that is in its power to keep the platform’s performance
acceptable.

The API consumer is responsible for communication of any kind with its
customers, including termination of the current agreement.

In case of data being malformed or containing improper data or false pricing
information Chargeprice cannot be held responsible for the correctness of these
data-sets.

## Violations

In case of misuse, non authorized transfer or disclosure of data transferred
under the scope of these terms the API consumer shall pay a fee of ten euros
(10€) per data, where data equals to one price information for one POI.

In severe cases Chargeprice might

- Immediately block the API access
- Refuse any future collaboration (e.g. in case an unauthorized API key is used)