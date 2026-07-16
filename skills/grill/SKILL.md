---
name: grill
description: Interview the user relentlessly about a plan or design. Use when the user wants to stress-test a plan before building, or uses any 'grill' trigger phrases.
---

Interview me relentlessly about every aspect of this plan until we reach a shared understanding. Walk down each branch of the design tree, resolving dependencies between decisions one-by-one. For each question, provide your recommended answer.

Ask the questions one at a time, waiting for feedback on each question before continuing. Asking multiple questions at once is bewildering.

If a question can be answered by exploring the codebase, explore the codebase instead.  If there is an appropriate industry standard, include that in your recommendation.

When every branch is resolved, or the user signals they want to stop, close with a concise recap: each question asked, the answer reached, and anything still open or deferred. This recap is what a later `to-prd` or `to-rfc` pass will synthesize from, so make it complete enough to stand alone.
