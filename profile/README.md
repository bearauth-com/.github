# BearAuth

Implement _your own_ authentication solution on the BearAuth API.

- [How is BearAuth used?](#how-is-bearauth-used)
- [What does BearAuth provide?](#what-does-bearauth-provide)
- [How is BearAuth different?](#how-is-bearauth-different)
  - [Better Security](#better-security)
  - [More Control](#more-control)
  - [Lower Cost](#lower-cost)

## How is BearAuth used?

BearAuth is a RESTful API that you invoke from your own backend service.

- Sign up for free at the BearAuth Portal.
- Get an API key.
- Deploy your auth solution in one of the following ways:
  - Add an `/auth` endpoint to your own API using a high-level SDK.
  - Setup a standalone service in AWS or Azure from a pre-made template.
  - Choose complete freedom and call the API any way you like.

## What does BearAuth provide?

A RESTful API:

- Users (PII obfuscation)
- Access (roles, groups, and permissions)
- Passwords (hashing, verification, recovery, strength enforcement, and breach detection)
- Time-based one-time passwords (generation and verification)
- public key cryptography (key generation, signing, and verification)
- Mock server (testing)

(Coming Soon) API SDKs for the following environments:

- Node.js
- Go
- Ruby
- Python
- Java
- C#
- PHP

(Coming Soon) High-level SDKs for the following authentication flows:

- Basic Auth
- Bearer tokens (JWT)

Consultation is also available if you want a custom solution hand crafted for your needs.

## How is BearAuth different?

With BearAuth, you own your own authentication solution, all for about the same effort and initial cost required to integrate with other SaaS solutions. Owning your own authentication solution provides better security, more control, and lower ongoing cost.

### Better Security

BearAuth uses [zero-access encryption](#what-is-zero-access-encryption) to protect your secrets. This means that even if BearAuth is compromised, your secrets are still safe.

Other solutions not only don't use zero-access encryption, but they technically can't do so. They have features like login pages that are end-user facing, so they have to be able to decrypt and use your secrets _independently._ This makes your secrets vulnerable to breaches and misuse.

BearAuth is focused on security, because it's all we do.

### More Control

The user experience is yours. Your website, your components, your design. No redirecting to a 3rd party login page with pay-to-remove 3rd party branding. No page refreshes to re-authenticate. No fiddling with custom domains pointing to a 3rd party. You just need a single `/auth` (or equivalent) endpoint in your own API, and we will even provide the implementation.

### Lower Cost

BearAuth is minimal. The simple and low ongoing cost we pass on to you reflects our focus. We don't need to try to amortize the cost of implementing, maintaining, and supporting a large feature set. Anything we don't do, is a middle markup you avoid.

As you implement your own authentication solution, we can provide consultation engagements for a one-time fee, all the way up to building the whole solution for you on your own infrastructure. Once you have your solution with the features you need, you won't need to pay for more than the ongoing cost of using the API.

We can also help you self-host the BearAuth API itself for a flat yearly support subscription fee. This can reduce your costs even further, while providing even more security and control.
