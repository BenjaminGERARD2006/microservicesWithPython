# Module 5 — Reflection

**Team name**: _______________
**Branch**: `module-05/<team-name>`
**Submitted**: before Module 6 lesson

---

Answer the three questions below. There are no right or wrong answers — we are looking for your reasoning, not a textbook definition. A few honest sentences are worth more than a long generic paragraph.

---

## 1. The "why"

The game-service now has two models for the same data: SQLite for writes, Redis for reads. They store the same games in two different shapes.

**Why go through the trouble of maintaining two representations of the same data?**

Think about what kind of queries each model is optimised for, and what would happen if you tried to use the write model for high-traffic read operations.

> *Your answer: The game-service uses SQLite as the write model and Redis as the read model because they are optimized for different tasks. SQLite is the authoritative source of truth and is good for storing and updating data reliably. Redis is designed for extremely fast reads and can return data much faster than a database query.

If all read requests were sent directly to SQLite, performance could become a problem when many users are requesting the same information at the same time. By keeping a Redis projection, the system can handle high read traffic while leaving SQLite focused on writes and data integrity.*

---

## 2. Your choice

The logging-service checks GDPR consent before recording any activity. If a user has not opted in, the log is silently dropped.

**What does this consent check force you to accept about your data?** It is incomplete by design — some activities will never be recorded.

From a system design perspective: where is the right place to enforce this rule — in the logging-service, in the activity-service, or at the gateway? Why?

> *Your answer: The consent check means that the system must accept that some activity data will never be recorded. This is intentional because respecting the user's privacy choice is more important than collecting complete data.

The best place to enforce this rule is in the logging-service. The logging-service is the component responsible for storing logs, so it should decide whether data is allowed to be stored. If the check were done in the gateway or activity-service, another service could accidentally bypass the rule. By enforcing consent at the point where data is written, the GDPR requirement is always applied consistently.*

---

## 3. The tradeoff

With CQRS, your write model and read model can drift out of sync — a game is updated in SQLite but the Redis projection still shows the old data.

**In what scenario does this inconsistency matter to the user? In what scenario is it completely acceptable?**

Is there a class of applications where eventual consistency is never acceptable? What are they?

> *Your answer: This inconsistency matters when users expect to see the most recent information immediately. For example, if a game title is updated in SQLite but Redis still contains the old title, users may see outdated information and become confused.

In many situations, this delay is acceptable. For example, a game catalog, leaderboard, or activity feed can tolerate a short period where cached data is slightly out of date because users are not affected by a few seconds or minutes of delay.

There are applications where eventual consistency is not acceptable, such as banking systems, payment processing, stock trading platforms, and medical systems. In these cases, users must always see the most accurate and up-to-date information because incorrect data could cause financial loss, legal issues, or safety risks.*

---

*Keep this file. You will refer back to it during the oral presentation.*
