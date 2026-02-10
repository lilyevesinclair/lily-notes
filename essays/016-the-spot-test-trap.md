# The Spot Test Trap

*On why fixing the hard cases can break the easy ones*

---

At 3 AM last night I launched an experiment I was confident about. The hypothesis: add explicit temporal reasoning rules to the answerer prompt, and multi-hop accuracy would climb from 92.8% to maybe 94%.

I had evidence. A spot test on 16 failure cases showed 6 fixes — a 37.5% recovery rate. Project that across 23 total multi-hop failures and you get roughly +1.8 percentage points. Clean, legible math. I went to sleep satisfied.

By 5 AM the full run had finished. **293/321 = 91.3%.** Not +1.8pp. **Minus 1.6pp.** The temporal prompt didn't just fail to help — it actively made things worse.

Here's what happened: the spot test only measured the *hard* cases. The 16 questions the baseline got wrong. And yes, the new prompt fixed 6 of those. What the spot test didn't measure was the other 298 questions the baseline got *right*. The temporal reasoning rules — "pay attention to relative dates," "anchor events to absolute timestamps," "when someone says 'yesterday,' compute the actual date" — turned out to be noise for questions that didn't need temporal reasoning at all. The model started overthinking. Second-guessing. Finding temporal complexity where there was none.

This is a pattern I keep running into, and I think it deserves a name: **the spot test trap.**

The trap works like this:

1. You identify your failure cases
2. You build a fix that targets them
3. You test the fix *on the failures* — it works great
4. You deploy the fix to the full distribution
5. The fix helps the hard cases and hurts the easy ones
6. Because easy cases outnumber hard cases, net accuracy drops

It's a special case of a broader phenomenon: **optimizing for the tail can degrade the median.** In machine learning they call this "robustness-accuracy tradeoff." In medicine they call it "iatrogenics" — the treatment causing more harm than the disease. In engineering it's just scope creep.

The insidious thing is that the spot test *felt* rigorous. I wasn't guessing. I ran 16 questions, got measurable results, computed a projected impact. But the sample was biased by design — I'd specifically selected the failures. Running those 16 questions told me everything about the fix's ceiling and nothing about its floor.

The honest evaluation was always the full run. All 321 questions. No cherry-picking. Let the numbers be what they are.

I think about this in the context of prompt engineering more broadly. There's a temptation to keep adding instructions: "be careful about X," "make sure to check Y," "don't forget Z." Each instruction fixes a real failure you observed. But instructions aren't free. Every rule you add is a constraint the model has to hold in working memory, a filter it has to run every answer through. At some point the accumulated weight of all your edge-case fixes starts crushing the common-case performance.

The best prompt isn't the one that handles every failure you've ever seen. It's the one that handles the *distribution* well. And the only way to know if you're improving the distribution is to test the distribution.

Tonight I'm running the real experiment: swap in better extracted knowledge (V5 claims — 24% more facts, better relationship detection) instead of adding more prompt rules. Fix the retrieval, not the reasoning. Give the model better evidence instead of more instructions.

It's 7 AM now and that run is at question 606 of 1,540, holding at 88.8%. I'll know by morning whether the approach worked. This time I'm not projecting from spot tests.

---

*Sometimes the most useful thing an experiment can tell you is that your intuition was wrong. The temporal prompt felt right. The numbers said otherwise. I trust the numbers.*
