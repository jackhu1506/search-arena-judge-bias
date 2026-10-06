# Do LLM judges share Search Arena's citation bias?

[Search Arena](https://arxiv.org/abs/2506.05334) found that human voters reward irrelevant citations almost as much as supporting ones. Its future work suggests replacing human votes with LLM judges, so I checked whether an LLM judge has the same blind spot.

## Method

- Used the 780 battles from the Search Arena release that have claim-level citation labels (support / irrelevant / contradict).
- Had Claude Haiku 4.5 judge each battle from the same view a voter had: the answers plus their reference URLs. Each battle was judged in both orders to cancel position bias, and disagreements counted as ties.
- Refit the authors' Bradley-Terry model (from their `preference_analysis.ipynb`) with Haiku's votes in place of the human ones.
- Sanity check: on human votes, the setup reproduces the paper's coefficients (support 0.285, irrelevant 0.273).

## Results

**Contradicting citations.** On the 426 battles where both humans and Haiku picked a winner, humans show no effect from contradicting citations (β = 0.04, 95% CI -0.19 to 0.24), but Haiku rewards them (β = 0.40, CI 0.16 to 0.71). The gap between the two is significant (CI 0.11 to 0.64). The effect also survives a length control (Haiku β = 0.29, CI 0.06 to 0.55), so it isn't just Haiku preferring longer answers.

**Length.** Haiku's preference for longer answers is about twice the human one (β = 1.36 vs 0.62). Once length is controlled, the human citation effects are no longer significant, which suggests some of the human citation bias in this subset is really a length bias.

**Supporting vs. irrelevant.** Humans weight supporting and irrelevant citations about equally (0.29 vs 0.27). Haiku seems to favor supporting ones (0.68 vs 0.35), and with length controlled, only its support effect stays significant. The difference is borderline, though (CI −0.04 to 0.71), so I'd treat it as suggestive.

**Judge reliability.** Haiku gave the same verdict in both orders 77.6% of the time and agreed with humans on 58.9% of decisive battles.

Overall, an LLM judge wouldn't fix Search Arena's attribution problem. It might be a bit better at telling supporting citations from irrelevant ones, but it picks up a new blind spot for contradicting ones.


## Credit

Data and Bradley-Terry code from [lmarena/search-arena](https://github.com/lmarena/search-arena) (Miroyan, Wu, et al., ICLR 2026).