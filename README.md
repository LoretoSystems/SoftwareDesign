# Software Design Patterns

The goal is not just to "see an example," but to:
- Comprehend **when** to use a pattern (and when not to).
- Practice **implementing** each pattern in idiomatic Java.
- Explore **trade-offs** between patterns solving similar problems.
- Build an intuition for applying patterns in real-world systems.

## Optimistic and Pessimistic Locking

I start with optimistic locking. Pessimist locking is next on the menu.
Both, optimistic and pessimistic locking are concurrency control mechanisms.
These are often implemented as software patterns with the purpose to manage database systems and applications to manage
how multiple processes interact with shared data.

These are not standalone principles.
They stem from broader concurrency control principles in distributed systems and database theory.
These mechanisms are commonly applied through patterns such as:

- **Optimistic Locking Pattern:**
Uses versioning to detect conflicts at commit time.
For instance, a version number or a timestamp are usually considered.

- **Pessimistic Locking Pattern:**
Involves acquiring database-level locks in order to block access by other transactions simultaneously.
For instance,  SELECT for UPDATE.

These patterns are helpful in order to implement isolation and consistency in line with
ACID (Atomicity, Consistency, Isolation, Durability) principles.

### Optimistic locking

#### What exactly is optimistic locking?
Optimistic locking can be harsh but the truth is simple.

Here lies the assumption that people won’t step on each other’s toes.
If they do, you’re in trouble.
You read the data. Then you make changes.
Now that you're done, at the last second, you check if someone else already changed it.
If they did? Your work gets thrown away.

No mercy.

If the data was persisted prior, for instance in memory or a persistent volume claim, there may be a way back.

Otherwise, you most likely restart from scratch, or tell the user:
"Something went wrong. Please try again."

Not a good solution.

It’s fast when comparing to pessimistic locking.
When conflicts are rare, it avoids the overhead of locking everything up front.
But when clashes happen, it wastes time and effort. 
For instance, forcing 5 application users to edit the same Google Doc without warnings to then
deleting 4 of their changes at save time.

#### Why should one consider Optimistic Locking?

A scenario where this solution should be considered is when one knows that updates are infrequent while
retries won’t break the user experience.

If conflicts are common, optimistic locking isn’t the solution.
Pessimistic locking most likely is.

By comparison, pessimistic locking locks the data the moment it is accessed while
blocking everyone else.

It certainly is slower. But certainly is certain.

Do not choose on hope.

Choose based on reality.

#### When should one consider to implement Optimistic Locking?
* **Assumption**
  * Conflicts are extremely rare and when they occur, you handle them gracefully.
* **Mechanism**
  * No lock is held during reads, instead, a **version column** for instance, is checked on write.
* **On conflict**:
  * The update fails as an `OptimisticLockException` is thrown.
The caller decides whether to retry or surface a conflict 'gracefully' to the user.
* **Potentially suitable for**:
    * High read with relatively low write contention.
    * Systems where retries are handled gracefully.

