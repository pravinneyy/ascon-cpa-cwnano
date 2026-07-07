# Chapter 1 — Abstract

*[← Back to README](../README.md) · Next: [02 — Background →](02_Background.md)*

---

## Summary

| | |
|---|---|
| **Target** | ASCON-128 (NIST Lightweight Cryptography standard), unprotected reference implementation |
| **Platform** | STM32F0 target on a ChipWhisperer Nano |
| **Attack class** | First-order Correlation Power Analysis (CPA) |
| **Data** | 3,000 power traces, 2,048 samples each, fixed key / random nonce |
| **Result** | 14 of 16 secret key bytes recovered |

---

## Abstract

The growing deployment of lightweight cryptographic algorithms on resource-constrained embedded systems has introduced a persistent gap between *algorithmic* security and *implementation* security. Modern lightweight ciphers are engineered to provide strong mathematical guarantees at minimal computational cost, but a mathematically sound cipher is not automatically a physically secure one. Many software implementations still leak information through the physical side effects of execution — power consumption, electromagnetic emission, timing — regardless of how carefully the underlying algorithm was designed. This distinction, between breaking a cipher and breaking its implementation, is the subject of this repository.

**ASCON**, selected by the National Institute of Standards and Technology (NIST) as the standard for Lightweight Cryptography (LWC) in 2023, provides Authenticated Encryption with Associated Data (AEAD) while remaining efficient enough for low-power embedded devices. Its algorithmic security has been extensively vetted through public cryptanalysis. What has received comparatively little public, reproducible documentation — particularly outside of academic papers — is how an *unprotected* software implementation of ASCON behaves under direct power analysis on cheap, widely available hardware. That is the gap this project fills.

This repository documents a complete, from-scratch implementation of a **first-order Correlation Power Analysis attack** against an unprotected ASCON-128 implementation executing on an STM32F0 microcontroller, using the **ChipWhisperer Nano** — the lowest-cost side-channel analysis platform commercially available. Almost all publicly available CPA walkthroughs target AES, because AES conveniently exposes an 8-bit S-box that can be attacked byte-by-byte with a lookup table. ASCON offers no such convenience: it is a **bit-sliced permutation built entirely from Boolean operations** (AND, XOR, NOT) operating across a 320-bit state. There is no S-box to copy a leakage model from. Every leakage equation used in this attack had to be derived by hand from ASCON's internal round function.

Rather than attempting to model the full 320-bit permutation at once — which would be both mathematically unwieldy and computationally infeasible — the attack targets the **first initialization round**, where the key has only just entered the state and diffusion has not yet spread it across many variables. The attack proceeds **progressively**: it first recovers the intermediate variable **y4**, uses that result to derive **x1**, and then uses x1 to construct a second, independent leakage model for **y2**. This staged approach converts one hard 128-bit recovery problem into a sequence of tractable 8-bit (256-hypothesis) problems, each with its own hand-derived Boolean leakage equation.

Traces were acquired under a **fixed secret key** and **randomly generated public nonces**, since CPA requires the unknown (key) to stay constant while a known input (nonce) varies across measurements. A total of **3,000 power traces**, each containing **2,048 samples**, were captured. Before running CPA, variance analysis was used to narrow the search from the full trace down to the ~650-sample window where the initialization permutation actually executes. Crucially, the sample of *maximum variance* was **not** used as the attack point — Pearson correlation was instead computed across the entire active window, and the sample of *maximum correlation for the correct key hypothesis* was selected instead. These two points turned out to be different, which is a central, non-obvious finding documented in detail in [Chapter 7](07_Trace_Capture.md) and [Chapter 9](09_CPA_Attack.md).

A **Hamming Weight** leakage model was adopted throughout, based on the standard CMOS assumption that instantaneous power draw correlates with the number of logical '1' bits being processed. For every one of the 256 possible values of each key byte, the corresponding hypothetical Hamming Weight of the targeted intermediate variable was computed and correlated against the measured power at every sample in the attack window, producing a 256 × N correlation matrix per byte from which both the correct key hypothesis and its leakage location could be read off simultaneously.

The attack recovered **14 of the 16 secret key bytes** using only 3,000 traces captured on hardware costing under $50. The two unrecovered bytes exhibited consistently weaker correlation than the rest, suggesting either insufficient trace count or a weaker leakage point rather than a flaw in the derived Boolean model — both avenues for improvement are discussed in [Chapter 11](11_Limitations.md).

Beyond the attack itself, this repository is built around **reproducibility**. Every stage — firmware selection and adaptation, hardware configuration, trace acquisition, leakage-model derivation, statistical analysis, and key recovery — is documented with the reasoning behind each decision, including the dead ends encountered along the way (Chapter 6 covers why the officially "protected" masked firmware could not be built for this target, forcing the switch to the reference implementation actually attacked here). The goal is not merely to show that the attack succeeded, but to leave behind a resource detailed enough that a student or researcher can reproduce every figure in this repository on their own hardware.

---

## Contributions

- A complete, from-scratch **first-order CPA attack** against the first initialization round of ASCON-128 — no existing AES-oriented tutorial could be reused, since ASCON has no byte-oriented S-box.
- Hand-derived **Boolean leakage models** for the ASCON intermediate variables `y4` and `y2`, expressed purely in terms of known nonce bytes, known IV bytes, and one unknown key byte per hypothesis.
- A **progressive recovery strategy** (`y4 → x1 → y2`) that reduces a 128-bit recovery problem into independent 8-bit sub-problems.
- A full **Pearson-correlation CPA implementation** using vectorized NumPy hypothesis matrices, avoiding explicit per-trace loops.
- A working adaptation of the **official ASCON SimpleSerial firmware** for the memory- and buffer-constrained ChipWhisperer Nano target.
- Empirical demonstration and documentation of the distinction between **variance-based** and **correlation-based** leakage localization — and why only the latter should be trusted to select the final attack window.
- A 3,000-trace experimental campaign resulting in the recovery of **14 of 16 secret key bytes**, with full numerical results in [Chapter 10](10_Results.md).
- Comprehensive, chapter-by-chapter documentation intended as a reusable educational reference for side-channel analysis of Boolean/ARX-style lightweight ciphers.

---

## Keywords

`ASCON` · `Lightweight Cryptography` · `NIST LWC` · `Side-Channel Analysis` · `Correlation Power Analysis` · `ChipWhisperer Nano` · `Hamming Weight Leakage Model` · `Pearson Correlation` · `Embedded Security` · `Cryptanalysis` · `Power Analysis` · `STM32F0`

---

*Next: [Chapter 2 — Background](02_Background.md), which introduces side-channel analysis from first principles for readers new to the field.*
