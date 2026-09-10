# Standards floor: the Fowler smell baseline

> Harvested from `code-review` (mattpocock/skills v1.2.3). Loaded on demand by the
> Standards portion of the adversarial lens; it is not in `SKILL.md` because it only applies to
> one lens and would otherwise ride in context on every run.

Most of the owner's repos document no coding standards at all, so a "does it follow the standards"
review has nothing to check against and quietly becomes a taste review. This is the floor that
applies **even when the repo documents nothing**: a fixed set of Fowler code smells
(*Refactoring*, ch.3), matched against the diff.

Two rules bind it:

- **The repo overrides.** A documented repo standard (`CODING_STANDARDS.md`, `CONTRIBUTING.md`,
  a project `CLAUDE.md`) always wins. Where the repo endorses something the baseline would
  flag, suppress the smell.
- **Always a judgement call.** Every item here is a labelled heuristic ("possible Feature
  Envy"), never a hard violation. Documented-standard breaches can be hard findings; baseline
  smells never are. Skip anything tooling already enforces (formatter, linter, type checker).

Each reads *what it is*, then *how to fix*. Match against the diff only, not the whole repo.

| Smell | What it is | Fix |
|---|---|---|
| **Mysterious Name** | A function, variable or type whose name does not reveal what it does or holds | Rename it. If no honest name comes, the design is murky |
| **Duplicated Code** | The same logic shape in more than one hunk or file in the change | Extract the shared shape, call it from both |
| **Feature Envy** | A method reaching into another object's data more than its own | Move the method onto the data it envies |
| **Data Clumps** | The same few fields or params keep travelling together, a type wanting to be born | Bundle them into one type, pass that |
| **Primitive Obsession** | A primitive or string standing in for a domain concept that deserves its own type | Give the concept its own small type |
| **Repeated Switches** | The same `switch` or `if`-cascade on the same type recurs across the change | Replace with polymorphism, or one map both sites share |
| **Shotgun Surgery** | One logical change forces scattered edits across many files in the diff | Gather what changes together into one module |
| **Divergent Change** | One file or module edited for several unrelated reasons | Split so each module changes for one reason |
| **Speculative Generality** | Abstraction, parameters or hooks added for needs the spec does not have | Delete it, inline back until a real need shows |
| **Message Chains** | Long `a.b().c().d()` navigation the caller should not depend on | Hide the walk behind one method on the first object |
| **Middle Man** | A class or function that mostly just delegates onward | Cut it, call the real target directly |
| **Refused Bequest** | A subclass or implementer that ignores or overrides most of what it inherits | Drop the inheritance, use composition |

**Speculative Generality carries extra weight here.** It is the owner's own rule twice over: the house rule
("no speculative abstractions") and the programmable-policy rule ("this modularity rule does not
justify speculative frameworks: define only the smallest interface proven by a live narrow path
and a second implementation or contract test"). The companion test:
**one adapter means a hypothetical seam, two adapters means a real one.** A seam introduced with
a single implementation behind it is Speculative Generality wearing an architecture costume.
