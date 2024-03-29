# BearAuth

Implement your own authentication quickly and easily. Foot-guns not included.

- [How do I use BearAuth?](#how-do-i-use-bearauth)
- [What does it do?](#what-does-it-do)
  - [Intro to authentication](#intro-to-authentication)
  - [Passwords hashes](#passwords-hashes)
  - [Time-based one-time password secrets](#time-based-one-time-password-secrets)
  - [Asymmetric keys (RSA, ECDSA)](#asymmetric-keys-rsa-ecdsa)
- [What is zero-access encryption?](#what-is-zero-access-encryption)
- [Why not a full SaaS identity solution?](#why-not-a-full-saas-identity-solution)
  - [Security](#security)
  - [Expense](#expense)
  - [Integration complexity](#integration-complexity)
  - [Customization limitations](#customization-limitations)
  - [Vendor lock-in](#vendor-lock-in)
- [Can I self host?](#can-i-self-host)

## How do I use BearAuth?

BearAuth is a RESTful API that you can call from any backend service. All you need is a free API key. If you can make an HTTP request, you can use BearAuth.

- Sign up for free at the [BearAuth Portal](#).
- Deploy a [complete authentication solution](#) to AWS in minutes.
- Learn and grow with the [OpenAPI Specification](#).

## What does it do?

BearAuth is designed to be a complete backend solution for building in-house authentication. By providing just the backend, BearAuth is able to focus on the most sensitive parts of authentication (removing the foot-guns). This lets you build the frontend and business logic that are unique to your application, without having to work around a third party.

You can think of it like "Authentication as a Service", "MFA as a Service", "Encryption as a Service", and "Signing as a Service", all rolled into one.

### Intro to authentication

Authentication is the process of verifying that a user is who they say they are. This is done by asking them to prove at least one of the following:

- Something they know (password)
- Something they have (TOTP secret, private key)
- ~~Something they are (biometrics)~~

> Biometrics are really just a way to protect "something they have", like a phone. Biometric data should _never_ be moved off of a physical device (or another closed system), because it's not something that can be changed if it is compromised.

The trick is that the user has to be verified without the verifier knowing the user's secrets. If the verifier knew the user's secrets, the verifier would be able to impersonate the user. BearAuth takes care of this automatically, hiding secrets from you, and even itself using zero-access encryption, while still making them easy to use.

There are three types of secrets that BearAuth supports:

- Password hashes
- Time based one-time password secrets (TOTPs)
- Asymmetric encryption keys

With these, you can quickly build Basic Auth, OAuth2, Passkeys, MFA, or nearly any other kind of authentication system you can imagine.

### Passwords hashes

A password must never stored in its original form. Instead it's stored as a salted one-way hash (delicious). The hash is also zero-access encrypted for defense-in-depth. The only way a hash can be used is by getting the original secret back from the user, passing it though the same hashing process, and then comparing the regenerated hash to the stored hash. The original secret is never stored on disk.

Hashing and storing passwords is relatively straight forward. However, the risks of doing it wrong are very high, and the real challenges are in maintaining the security of passwords over time.

- Hashes must be regenerated as technology evolves, so that the cost of cracking a password if the hash is accidentally exposed, remains prohibitively high.
- Password requirements must be enforced so that users don't set weak passwords that are easy to guess.
- Passwords may need to be automatically generated and/or expire if they are temporary.
- Password verifications must be rate limited to prevent brute force attacks.
- Users frequently forget their passwords, so a solution for setting (recovering) new passwords must be implemented.

BearAuth does this all almost automatically. Password resets require a little extra work on your part in order to send email or sms messages to the user. However, the reset process is managed by BearAuth, so sending messages is really the only thing you need to do. We hope this is actually a good thing, because it means you have complete control over the user experience, and you can leverage your existing messaging infrastructure.

In addition to being the correct way to "store passwords", hashing is also a useful way to store API keys, license keys, and other secret types that only need to be verified and not retrieved.

### Time-based one-time password secrets

A time-based one-time password is a numeric code, usually 6 digits, which is derived from a secret shared by two parties. After the secret is initially shared, usually through a QR code, the secret is never shared again. To verify that the two parties both know the secret, they both use the secret and the current time to generate a new code, and then the codes are compared. If they both generated the same code for the same time, then both parties know the same secret.

The TOTP algorithm is even easier to implement than password hashing. The difficulty is that unlike a password, the shared secret has to be retrievable. That means that a data breach could expose TOTP secrets.

BearAuth generates and manages TOTP secrets for you. We'll even generate the QR code image! The secret is zero-access encrypted before it is stored, so not even BearAuth can use it without your credentials. The secret is used by calling the BearAuth TOTP API verification endpoint.

TOTP is one of the most popular forms of MFA, because it does not require a network connection, doesn't incur messaging fees, and it's easy to use.

### Asymmetric keys (RSA, ECDSA)

Using asymmetric keys refers to having a pair of keys where one key is public and one is private. The public key can be given to anyone without compromising security, but the private key must be kept secret. Data signed with the private key can only be verified with the public key, and data encrypted with the public key can only be decrypted with the private key.

BearAuth generates asymmetric keys for you, and gives you the public key. You do not get the private key, which is never exposed for any reason. The private key is zero-access encrypted before it is stored, so not even BearAuth can use it without your credentials. The private key is then used by calling the BearAuth Keys API sign and decrypt endpoints.

Asymmetric keys are commonly used for JWT signatures. They can also used for encryption and decryption, for example encrypted JWT tokens.

## What is zero-access encryption?

Zero-access encryption means encryption that nobody but the owner has the technical ability to decrypt. BearAuth might store your secrets, but they aren't ours to use or give away.

## Why not a full SaaS identity solution?

- Security
- Expense
- Integration complexity
- Customization limitations
- Vendor lock-in

### Security

Identity and auth solutions are prime targets for attackers. They contain user information and credentials which allow bad actors to impersonate and extort. Think of it like your identity solution being the bank vault that everyone wants to rob. BearAuth uses zero-access encryption to protect those secrets, even from itself.

Other solutions not only don't do this, but they technically can't do this. Because they have features like login pages, that are end-user facing. They have to be able to decrypt and use secrets independently. This means that everything an attacker wants is stored in one place, and probably for a lot of different customers.

BearAuth is designed so that you have to provide credentials with every call to the API. Without those credentials, BearAuth can't decrypt anything you own. Compromising your secrets now requires attacking two separate systems, in limited window of time, for a much smaller gain.

### Expense

Identity and auth solutions try to out compete each other by adding more features, until any single customer is really only going to use a fraction of them. The more features they have, even in potential, the more expensive the solution will be over time.

Economically it makes sense for the feature-rich vendors to prioritize larger customers who use more features. Eventually, their pricing model will change to reflect that preference for bigger fish. You can see this pattern in effect with most SaaS solutions. Their free tiers tend to disappear or be reduced as they grow, their pricing becomes more complex, licenses become more restrictive, and support channels are reduced or become less responsive to smaller customers.

### Integration complexity

All those features also make the product more complex. The more complex the product is, the more they want you to focus on their supported way of doing things. Otherwise, the interactions between all those features would cause the number of support scenarios to explode. This will tend to force design choices on you that you may not want to make.

As an example, most solutions will nudge you toward an OAuth flow. OAuth has deprecated the flow that allows credentials to be directly exchanged for a token without a page redirect. So, you need to support redirection away from your page at any time if the user needs to re-authenticate. In theory, there are workarounds for this (ie. PKCE), but in practice it only partially mitigates the problem and adds an extra piece of sensitive information that an untrusted client needs to protect. This is to avoid asking the user for credentials again, which would be more secure, but require the redirect.

You really don't need OAuth if you only have a single application and you don't need to support third-party applications. Even if you do want OAuth, there are valid shortcuts you can take to provide a better user experience if you are still planning to be the identity provider.

Even large companies that implement their own identity solutions, like Google, Facebook, and Microsoft, still customize them and reinterpret the OAuth specs to meet their own needs. You can do the same, without having to build the tough parts from scratch.

### Customization limitations

Increased solution complexity and features (ironically) do not mean greater customization. In fact, they may very well mean the opposite. Attempting to limit complexity while adding more features leads to treating customization as the enemy.

You will frequently see this in the form of an SDK that you are expected to use when interacting with the service. The SDK may do some magic that the provider thought was too complex to explain or document. Or, it may help prevent uses that falls outside the approved use cases. SDKs are not always available for all languages or platforms. They also make it harder for the vendor to offer new features, because any new feature includes the overhead of updating all of the supported SDKs.

There are also vendor provided UIs, which will be limited in customization scope. If you want API access to the data stored by the vendor, you may find there are significant limitations in how it can be used, to the point where you have to build a wrapper for it, or a mirror of it.

### Vendor lock-in

This is more of a summation than an additional draw back. The more you sink costs into integrating with, designing around, and customizing a solution, the more you want to avoid losing the perceived value you've gained, and the more you want to avoid having to do it all over again.

You might think back and wish you'd just built some of it yourself. Of course, that wasn't really an option, because gradual or partial adoption isn't really a thing with most solutions.

It doesn't have to be that way. Let us do the parts we're really good at, and you can take what you need. You will build a little extra that will meet your needs better than a one-size-fits-all solution, and we'll make it painless.

## Can I self host?

Yes you can. It's a bit more work, but we know that sometimes you have to have things in house. We're committed to providing resources to make it as easy as possible. And, we'll be with you through upgrades, maintenance, and all your future requirements.

Please reach out to us on the [BearAuth Portal](#) for more information about self hosting and enterprise support.

---

- IDs
  - POST `/v1/ids`: Generate a new random ID.
  - PUT `/v1/id/<id>`: Add or replace an ID.
  - GET `/v1/id/<id>`: Get contact information associated with an ID.
  - GET `/v1/ids?q=<query>`: Get IDs matching a query (ie. email, phone).
  - PATCH `/v1/id/<id>`: Update contact information associated with an ID.
  - DELETE `/v1/id/<id>`: Delete an ID.
- Secrets
  - PUT `/v1/id/<id>/secret/<name>`: Add or replace a secret.
  - PUT `/v1/id/<id>/secret/<name>/verify`: Verify a secret.
  - GET `/v1/id/<id>/secret/<name>`: Get public secret information.
  - GET `/v1/id/<id>/secrets`: List all secrets for an ID.
  - PATCH `/v1/id/<id>/secret/<name>`: Update public secret information.
  - DELETE `/v1/id/<id>/secret/<sid>`: Delete a secret.
  - DELETE `/v1/id/<id>/secrets`: Delete all secrets for an ID.