# Module 6 — Reflection

**Team name**: _______________
**Branch**: `module-06/<team-name>`
**Submitted**: before Module 7 lesson

---

Answer the three questions below. There are no right or wrong answers — we are looking for your reasoning, not a textbook definition. A few honest sentences are worth more than a long generic paragraph.

---

## 1. The "why"

The gateway now validates every JWT before forwarding a request. Individual services no longer need to check identity themselves.

**What does centralising authentication at the gateway buy you?** What would the alternative look like — if every service validated tokens on its own?

Think about what happens when you need to rotate the secret key, or add a new service to the system.

> *Your answer: Centralising authentication at the gateway means that token validation only needs to be implemented in one place. Every service can trust that requests reaching them already come from an authenticated source. This makes the system easier to maintain because changes such as rotating the secret key or updating authentication logic only need to be made at the gateway.

If every service validated tokens on its own, the same code and configuration would have to be duplicated across multiple services. Adding a new service would require implementing authentication again, which increases maintenance effort and the risk of inconsistent security rules.*

---

## 2. Your choice

When activity-service calls user-service internally, it uses a Machine-to-Machine (M2M) token — not a user's token.

**Why can't it just reuse the user's token that arrived in the original request?**

What would break, or what door would you accidentally leave open, if services passed user tokens between themselves?

> *Your answer: The activity-service should use an M2M token instead of reusing the user's token because it is acting as a service, not as the user. The user only authorized the original request, not every internal call that might happen afterward.

Passing user tokens between services would increase the attack surface and could allow services to impersonate users unnecessarily. It would also tightly couple internal communication to user sessions. Using a dedicated service token makes responsibilities clearer and follows the principle of least privilege.*

---

## 3. The tradeoff

The gateway and the auth-service share the same `SECRET_KEY` to verify tokens without making a network call on every request.

**What is the security risk of sharing this key?** What happens if it leaks?

And what would the alternative look like — verifying tokens by calling auth-service on every request instead? What does that cost you?

> *Your answer: The main risk of sharing the SECRET_KEY is that if the key leaks, an attacker could create valid JWTs and impersonate any user or service in the system. Because both the gateway and auth-service trust the same key, a compromise would affect the entire authentication system.

The alternative would be for the gateway to call auth-service to verify every token. This would be more secure because the signing key would only exist in auth-service, but it would add network latency and create a dependency on auth-service being available. If auth-service went down, authentication for the whole system could fail. Sharing the key improves performance and availability, but it increases the impact of a key compromise.*

---

*Keep this file. You will refer back to it during the oral presentation.*
