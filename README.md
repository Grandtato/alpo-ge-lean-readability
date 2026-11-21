# alpo-ge-lean-readability
# Intermediate `Prop` Definitions in the alpo.ge Lean Proof of the Riemann Hypothesis

**Juan Bermudez** — 21 November 2026

These are the current facts: the alpo.ge proof of RH [1] has been certified. The Lean and Mathlib communities, together with the Lean FRO and the Mathlib Initiative, have confirmed that both the statement and the proof are correct [2, 3]. I am glad to see the community engage so quickly, and I respect its conclusion. I would, however, like to raise a point that these checks cannot address: what the intermediate steps actually say.

Many steps in the archive, including the entire main line, are stated only by name: `def Name : Prop := …`, followed by `theorem step : Name`. As a hypothetical illustration of the general mechanism (not claiming this of the present proof), a named statement can be empty and still pass every check. Checked on Lean v4.33.1 (the version the archive uses):

```lean
-- Hypothetical illustration of my own (not from the alpo.ge archive)
def MyBigClaim : Prop := True
theorem toy_step : MyBigClaim := trivial
#print axioms toy_step   -- 'toy_step' does not depend on any axioms
```

The compiler checks the definition hidden behind the name, never what the name promises. By contrast, when a theorem is written out in full, the mathematics itself is what passes the compiler: what you read is exactly what was checked, so no doubt remains.

How heavily does the archive rely on names? Using the same local script search (assisted by Claude) on both (Mathlib v4.33.1, commit `0df444a`, the version the archive pins [4]):

- The archive contains 46,911 definitions of the form `def Name : Prop := …`; Mathlib contains only 152. Per 1,000 theorems that is 57 against 0.8, so the pattern occurs about 70 times more often in this proof.
- More to my point, about 27,000 theorems in the archive have one of these names as their entire statement, as in `theorem allChi : UpperTheory.EveryCharacter`. In Mathlib, the same search finds none: its theorems either write the statement out or apply a concept to its arguments, as in `IsPGroup p G`. This is because Mathlib's 152 are almost all general concepts that take parameters (`Convex`, `IsPGroup`, `LinearIndependent`), plus two famous named statements (`RiemannHypothesis`, `FermatLastTheorem`) that Mathlib states as targets but does not prove.

Turning to real cases in the archive itself (which do not break the RH proof, since I am only raising a readability point), the names do not always match what they contain:

- `OpenLiteratureStatements` has the fields `caraianiLocalGlobalDual` and `galoisRepOfPolarizedPairLit`. They sound like two distinct published results, yet both have the same type, `UpperTheory.One.M.Row`, and the main line fills both with the same proof.
- A geometric condition defined as `fun _ _ _ => True` (`anyGeometry`, in `OverIx.lean`) is passed three times (via `geoCl anyGeometry`) in `SiegelEisensteinSecondTermHypotheses.lean`, immediately above the main-line theorem `every_character_of_theta_integrals_local_matching`. J. L. Nielsen was the first to point this out [5] (unlike [5], I am not arguing this invalidates the proof, only that this geometric slot does not carry the weight its name suggests).

Again, none of this calls the validity of the proof into question; an empty named step cannot make the final theorem false. It does mean that no one can tell which steps carry the mathematics without opening every definition. As the authors of the Carleson formalization put it, the Lean kernel "cannot ascertain that all definitions and theorem statements match their counterparts in the blueprint; this requires manual effort" [6, §2.5]. They divided their proof into 179 steps with human-readable statements, written before the proofs [6, §3].

My recommendation: in machine-generated proofs, the intermediate steps should be theorems with their full statements written out. Is anyone building a tool that starts from the final theorem, identifies every step the proof actually uses, and prints each statement in full?

In short: the proof is certified correct, but no one can yet see which of its steps carry the mathematics. With a named statement, the reader sees only a name; the compiler checks the definition behind it, never what the name promises. With a theorem written out in full, what the reader sees is exactly what the compiler checked.

## References

[1] L. Alpöge, *Remark*, 20 November 2026, https://alpo.ge/remark.pdf. Lean archive `lean.tar.gz` in 14 parts on alpo.ge; sha256 of the joined file `9631b4f020199e580ba86178cb56c6e9b09f690c2369a80dc4334aa93f49c7fc`.

[2] Lean and Mathlib communities, endorsed by the Lean FRO and the Mathlib Initiative, "Lean verification of the Riemann Hypothesis", Lean community blog, 21 November 2026, https://leanprover-community.github.io/blog/posts/riemann-hypothesis/.

[3] A. Sutherland, MIT verification page for the alpo.ge release, November 2026, https://math.mit.edu/~drew/grh/.

[4] The Mathlib Community, Mathlib, tag v4.33.1, commit `0df444a360eaa60ab8c11dca51a86af692955474`, https://github.com/leanprover-community/mathlib4.

[5] J. L. Nielsen, "The Invalidity of Alpöge's Proposed Proof of the Riemann Hypothesis: Vacuously true geometric and lattice witnesses in the Lean formalization", PhilArchive NIEIOT-2, 20 November 2026, https://philpapers.org/rec/NIEIOT-2.

[6] L. Becker, M. I. de Frutos-Fernández, L. Diedering, F. van Doorn, S. Gouëzel, E. Karunus, E. van de Meent, P. Monticone, J. Mulder-Sohn, J. Portegies, J. Roos, M. Rothgang, J. Sundstrom, J. Tan, "Formalizing Carleson's Theorem in Lean", arXiv:2609.31334 [math.CA], 2026, https://arxiv.org/abs/2609.31334.
