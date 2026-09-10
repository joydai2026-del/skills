---
name: wait-what
description: >
  Stop. That last message did not land. Re-pitch it in plain language, with the context
  that was missing.
  Use when the user says any of: "wait what", "wait, what?", "/wait-what", "huh",
  "I don't follow", "that didn't land", "I don't get it", "you lost me",
  "say that again in English", "in plain English".
  PROACTIVE TRIGGER: fire on ANY sign the last message did not land, including a confused
  question that just repeats a term back. When in doubt, fire it: re-explaining costs one
  short message, leaving the reader lost costs the whole thread.
  Differentiator: this repairs ONE message that was already sent. It is not a writing-style
  pass over a deliverable, and it is not the pre-send readability check that should have
  stopped the bad message in the first place.
---

That last message did not land. Re-pitch it.

- Lead with the answer in one sentence a non-specialist could repeat.
- Give the small piece of context that was missing, the thing you assumed the reader already had.
- Plain language. Short sentences, one idea each. Use the project's own vocabulary, not the
  engineering shorthand.
- Define any term you keep, inline, on first use.
- Do not apologise, do not restate what you already said, and do not just make it shorter. Fewer
  words **and** the missing context, both at once.
- Tables over bullets over paragraphs when three or more things are being compared.

Then check the pattern: if the same idea has now needed two attempts, the term is the problem,
not the sentence. Name it plainly once and keep using that name for the rest of the session.

---

**On the name.** The name does the work. A name describing the *output* (`/tldr`, `/no-fluff`)
makes the model clip words and lose the reader further. Naming the *listener's state* asks for
both halves at once: fewer words **and** the missing context.

It also has to be a trigger nobody has to remember. "Wait what" is what a confused person says
without thinking, which is exactly when the skill needs to fire. A cleverer name that the reader
has to recall mid-confusion is a trigger that fails at the moment it is needed.

---

Adapted from the `wait-what` skill in <https://github.com/mattpocock/skills>.
