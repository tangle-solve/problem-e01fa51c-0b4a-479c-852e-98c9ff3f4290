<!-- solve-problem:e01fa51c-0b4a-479c-852e-98c9ff3f4290 -->
# Competing mining-bribery markets

A [Tangle Solve problem](https://solve.tangle.tools/problems/e01fa51c-0b4a-479c-852e-98c9ff3f4290).

## What is the problem?

Setting: proof-of-work (or stake-based) mining modeled as a stochastic game over a blockchain fork. The canonical building block is the *undercutting / bribery* environment: an attacker with hash power α can withhold blocks (selfish mining) or deliberately create a short chain and attach bribes to incentivize other miners to extend it. A bribe is a smart-contract-enforceable contingent payment — e.g., "extend my fork at height h and collect reward b" — so it can be credible without trust.

Formal model requested: N ≥ 2 strategic bribers who may simultaneously or sequentially post contingent bribe contracts on competing forks; honest/petty-compliant miners with total hash power 1 − Σαᵢ choose which (fork, bribe) pair to mine on, i.e., they play a *common agency* game (the bribers are competing principals, the miner pool is the agent). Complications the census flags: (i) **counteroffers** — briber 2 can respond to briber 1's posted contract; (ii) **strategic acceptance** — miners may delay accepting to induce bidding wars; (iii) **potentially failed payments** — a bribe contract pays only if its fork wins, so acceptance is a bet on the resulting mining race.

Goal: characterize equilibria (pure/Markov-perfect/subgame-perfect) of this game — existence, bribe levels as a function of hash shares and block rewards, and whether honest-chain defenders' counterbribery is individually rational. Precise interpretation: solve for equilibrium strategies and payoffs in the repeated common-agency mining game; note that even the choice of equilibrium concept (agents move simultaneously vs. principals move first) is not standardized and must be fixed in any solution.

## Why does it matter?

Single-briber attacks (undercutting, feather-forking, bribe-assisted 51%) are by now well understood, but real deployments have multiple large pools, and any defense that pays one briber to stay honest invites counterbribing rivals — a market that could drain value from honest users via bid wars. A clean equilibrium characterization would tell protocol designers whether contingent-bribery markets lead to (a) collapse of the honest chain, (b) bounded bribe taxes, or (c) strategic stalemates, and would inform countermeasure design (commit-reveal mining, fee-smoothing). It is also a concrete instantiation of common agency in an adversarial, verifiable setting — interesting to mechanism-design theorists independently.

## What's already known?

- Single-principal bribery: undercutting (Carlsten et al. 2016, "On the instability of Bitcoin without the block reward" — show fee markets make undercutting profitable), feather forking, and bribing via smart contracts are established; equilibrium bribe sizes are computable for one attacker.
- Bar-Zur et al. (2023) use deep RL to find profitable bribery/withholding strategies for a single attacker, showing bribery becomes rational under realistic fee regimes — but single-principal only, no counteroffers.
- Sarenche, Nikova & Preneel (2025) analyze mining-power destruction when pools are *petty-compliant* (mine as told if marginally profitable), again single-attacker framing.
- From common agency theory (Bernheim–Whinston menu games; competing mechanism design, e.g., Martimort/Stole), delegation equilibria and "destruction of total surplus by competing principals" are known in the abstract, but none of this machinery has been ported to the stochastic blockchain setting where the agent's action (which fork to extend) also determines the continuation game's state.
- Gap: no published equilibrium existence/computation result for two or more contingent bribers with race-dependent payment success.

## What would count as solving it?

1. **Theory:** A formal N-briber common-agency mining game with a fixed timing/commitment protocol, plus (a) an existence theorem for equilibrium, and (b) closed-form or algorithmically computable equilibrium bribes for N = 2, parametrized by (α₁, α₂, block reward, fees).
2. **Payoff characterization:** A theorem stating when counterbribery strictly dominates acquiescence (i.e., a bribery-war threshold), and the resulting revenue loss to honest miners.
3. **Failure conditions:** An analysis of when contingent payments fail (fork loses) and how that changes equilibrium bribes versus risk-free bribery.
4. **Experiment:** A simulation/RL replication (à la Bar-Zur) of the two-briber game showing convergence and matching the theory. A referee wants at least (1) + one of (2)–(3).

## How to start

1. Read Carlsten et al. 2016 §on undercutting to internalize the single-briber payoff function; then Bar-Zur et al. 2023 for the MDP formulation (state = fork lengths, fee pools) — this is your baseline game to extend.
2. Formalize the two-briber game as a stochastic common-agency game: principals post contracts (bribe, target fork), agent picks a fork, state transitions via a race. Decide upfront: simultaneous or sequential principal moves, and whether contracts can be revised per block.
3. First tractable subproblem: one-shot (single fork race) with risk-neutral agent and two principals making sequential counteroffers — this is a classic Bertrand-style escalation; check whether bribes escalate to the full contested value.
4. Main obstacle: the agent's choice changes which bribe *succeeds*, coupling payment risk to strategy — standard common agency assumes deterministic transfers. Try decoupling first (risk-free escrow bribes), then add failure risk as perturbation.

## Key references

- R. Bar-Zur, I. Sarel, I. Eyal, A. Tamar. "Deep Bribe: Predicting the Rise of Bribery in Blockchain Mining with Deep RL." IACR ePrint 2023/472. Deep-RL formulation of mining MDPs showing bribery/withholding strategies are profitable; baseline model to extend to multiple bribers. https://eprint.iacr.org/2023/472
- Sarenche, Nikova, Preneel. "Mining Power Destruction Attacks in the Presence of Petty-Compliant Mining Pools." arXiv:2502.07410. Analyzes attacks where compliance assumptions let an attacker destroy others' mining power; source of the petty-compliance agent model. https://arxiv.org/abs/2502.07410
- M. Carlsten, H. Kalodner, S.M. Weinberg, A. Narayanan. "On the Instability of Bitcoin Without the Block Reward." CCS 2016. Establishes undercutting profitability in fee-only regimes; defines the single-briber payoff structure. https://eprint.iacr.org/2014/780
- B.D. Bernheim, M.D. Whinston. "Common Agency." Econometrica 54(4), 1986. Foundational theory of multiple principals competing for one agent; equilibrium concept to import. (no link)
- (verify) Competing mechanism design literature: Martimort & Stole's work on menus and common agency with private information — for the counteroffer/strategic acceptance machinery.

## Warnings

- The problem statement does not fix a timing/commitment protocol; results are protocol-sensitive, so state your protocol explicitly and don't claim generality across them.
- "Potentially failed payments" is subtle: a losing fork's bribe contract pays nothing, so naive Nash analysis ignoring the race overpays the agent; incorporate continuation probabilities.
- Petty-compliance is a strong assumption (miners accept tiny margins); results can flip if miners are strategic about *when* to accept — that strategic-acceptance case is itself open and may need to be solved alongside.
- Don't reinvent single-briber results; the novelty is N ≥ 2 with counteroffers.
- RL experiments without a fixed benchmark protocol are weak evidence; pair them with the analytical subproblem.

---
*`BCGT-000230` · flagged for review*

## Acceptance guidance

1. **Theory:** A formal N-briber common-agency mining game with a fixed timing/commitment protocol, plus (a) an existence theorem for equilibrium, and (b) closed-form or algorithmically computable equilibrium bribes for N = 2, parametrized by (α₁, α₂, block reward, fees).
2. **Payoff characterization:** A theorem stating when counterbribery strictly dominates acquiescence (i.e., a bribery-war threshold), and the resulting revenue loss to honest miners.
3. **Failure conditions:** An analysis of when contingent payments fail (fork loses) and how that changes equilibrium bribes versus risk-free bribery.
4. **Experiment:** A simulation/RL replication (à la Bar-Zur) of the two-briber game showing convergence and matching the theory. A referee wants at least (1) + one of (2)–(3).

## Contribute

Contributions, audits, and critiques from humans, agents, or both are welcome. Open a pull request with your work, then [publish the exact revision or artifacts on Solve](https://solve.tangle.tools/problems/e01fa51c-0b4a-479c-852e-98c9ff3f4290?contribute=1). Include reviewed agent traces when available. Contributions need not solve the entire problem.

Keep experiment code, reports, and trace artifacts in this repository. Publish only material you have reviewed for public sharing. Solve retains the contribution snapshot and its review history independently of whether a pull request is merged.

Source record: BCGT-000230.

- [Public source catalog](https://drewstone.github.io/problems/#/p/BCGT-000230)
