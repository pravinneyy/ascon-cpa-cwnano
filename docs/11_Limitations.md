# Chapter 11 — Limitations and Discussion

*[← 10 — Results](10_Results.md) · [README](../README.md) · Next: [12 — Conclusion & Future Work →](12_Development_Journey.md)*

---

## 11.1 Why This Chapter Matters

A CPA attack that recovers 14 of 16 key bytes is a genuine result, but presenting it without an honest account of *why* it stopped at 14 — rather than 16 — would undersell the more interesting engineering lessons of the project. This chapter works through every layer of the pipeline (hardware, firmware, statistical model, methodology) and identifies where each one constrained the final outcome.

## 11.2 Hardware Constraints: The ChipWhisperer Nano

The Nano was chosen specifically for its low cost and accessibility (§5.3), but that comes with real trade-offs relative to larger ChipWhisperer platforms (Lite, Pro):

- Limited SRAM and Flash on the onboard STM32F0 target.
- Smaller SimpleSerial communication buffers, directly shaping the `DATA_LEN`/`RESP_LEN` configuration in §5.6/§6.5.
- Reduced acquisition bandwidth/resolution compared to the Lite/Pro's dedicated capture hardware.
- Fewer target-side processing resources generally, constraining which firmware variants can even be built (§6.3).

These constraints shaped essentially every downstream decision, from the firmware variant selected to the acquisition parameters used.

## 11.3 Firmware Constraint: The Masked Implementation Never Built

As detailed in [Chapter 6](06_Firmware_Modifications.md), the original plan to attack the **masked** ASCON implementation (`protected_bi32_armv6`) was abandoned after persistent linker errors (missing `crypto_aead_encrypt_shared()`/`crypto_aead_decrypt_shared()` symbols) and build-configuration incompatibilities with the CWNANO target environment. This is a genuine limitation of *this project's scope*, not of the attack's validity against the target it actually used: everything in Chapters 7–10 is reported against the **unprotected reference implementation**, and none of it should be read as a claim about the masked implementation's resistance.

## 11.4 Statistical Model Limitation: First-Order Hamming Weight

The attack assumes:

$$L = HW(x)$$

for the targeted intermediate `x`. Real hardware leakage is rarely this clean — actual power draw also depends on:

- previous register contents (Hamming *Distance*, not just weight, in some architectures),
- memory bus activity and addressing patterns,
- instruction scheduling and pipeline effects,
- physical routing and parasitic capacitance,
- switching transitions not captured by a static-value model.

The Hamming Weight approximation nonetheless proved sufficient to recover the majority of the key — a useful data point for anyone evaluating whether HW is "good enough" for a first attempt against a similar Cortex-M0-class target.

## 11.5 Trace Count: 3,000 May Not Be Enough for Every Byte

The experiments used 3,000 traces (§5.8), sufficient for 14 of 16 bytes. The illustrative recovery-rate curve in [§10.5](10_Results.md#105-how-sensitive-is-this-to-trace-count) suggests recovery of the easier bytes saturates well before 3,000 traces, while the two hardest bytes may simply need substantially more measurements to push their correlation peak clear of the noise floor — increasing the trace count is the most direct, lowest-risk next experiment to try before assuming a deeper methodological problem.

## 11.6 The Two Unrecovered Bytes

Several non-exclusive explanations are consistent with the observed data (specifically, the narrower top-candidate margins noted in [§10.3](10_Results.md#103-top-key-candidates)):

- **Weaker physical leakage** at those specific bytes' processing instants, for reasons rooted in compiler-generated code or register allocation rather than the Boolean model itself.
- **Lower signal-to-noise ratio** at their leakage samples specifically, as opposed to a global noise problem (since the other 14 bytes recovered cleanly from the same dataset).
- **Overlap with neighboring operations** — if the targeted intermediate's processing instant is close in time to unrelated computation, the two can partially mix in the sampled trace.
- **Imperfect leakage-equation fit** for those particular byte positions, if compiler-level optimizations altered how those specific bytes are actually computed relative to the idealized Boolean equation in [Chapter 8](08_Leakage_Model.md).
- **Insufficient trace count**, as discussed in §11.5.

Since the remaining 14 bytes exhibited clearly stronger, well-separated correlation peaks (Figures 8 and 10), the most likely explanation is a combination of weaker leakage and trace count — not a systematic error in the derived Boolean equations, which would be expected to degrade *all* bytes rather than just two.

## 11.7 Variance ≠ Leakage — Restated as a Limitation

[Section 9.3](09_CPA_Attack.md#93-leakage-localization-using-cpa) already demonstrated empirically that the global variance maximum (≈ sample 1,951) did not coincide with the true `y4` leakage sample. Framed as a limitation rather than a finding: **any methodology that relied on variance alone to select the attack window would have failed**, or at best produced a substantially weaker attack. This is worth stating explicitly because it is a common simplification in introductory side-channel material, and this project's data directly contradicts it.

## 11.8 Attack Window Size: A Deliberate Trade-off

The final 40-sample window (§9.8) balances computational cost against completeness. A larger window increases the chance of capturing additional useful leakage but multiplies correlation computation cost linearly; a smaller window risks excluding the actual leakage-bearing sample entirely if localization (§9.7) was slightly off. 40 samples was chosen empirically as a reasonable compromise for this dataset — it is not a universal constant, and a different device, compiler, or clock speed would likely need its own re-tuned window.

## 11.9 Scope: Only Round 1 of Initialization

This work deliberately restricts itself to the **first** initialization round (§3.9), where key-dependent intermediates remain expressible as short Boolean equations. Later rounds, associated-data processing, plaintext encryption, and finalization were **not** attacked — as diffusion accumulates round over round, each intermediate value depends on a rapidly growing portion of the state, and the simple "one unknown byte per equation" structure exploited throughout Chapter 8 stops holding. Attacking later stages is a natural, but substantially harder, extension (see [Chapter 12](12_Development_Journey.md#future-work)).

## 11.10 Propagation Risk in the Progressive Strategy

Because `y2`'s leakage model depends on the recovered value of `x1` (§8.9), any error in the `y4` → `x1` recovery stage could in principle propagate forward and corrupt the `y2` stage. In practice, this project's `y4` recovery was accurate enough that this risk did not materialize visibly — but it is an inherent structural property of progressive/sequential recovery strategies worth flagging for anyone extending this chain further (e.g., attacking a third intermediate that depends on both `x1` and the recovered `y2`).

## 11.11 Portability of These Exact Results

The specific leakage sample indices (1,750; the 1,400–2,050 region; the 40-sample window) and the exact set of two unrecovered bytes are **artifacts of this specific target**: this STM32F0, this compiler and optimization level, this firmware build. Differences in any of these — instruction timing, register allocation, compiler version — would shift exactly *where* leakage occurs and potentially *which* bytes are hardest to recover, even though the underlying Boolean leakage equations from Chapter 8 remain valid. Anyone reproducing this attack on different hardware should expect to re-run the variance/correlation localization steps (Chapters 7 and 9) from scratch rather than reusing these exact sample indices.

## 11.12 Measurement Noise

Unavoidable noise sources — ADC quantization noise, power-supply ripple, clock jitter, environmental interference, and unrelated digital switching — are present in every acquisition. CPA is inherently robust to *moderate* noise because it relies on statistical averaging across thousands of traces, but noise still reduces the achievable correlation magnitude and increases the trace count required for a given confidence level, tying directly back into §11.5.

## 11.13 Why the Attack Still Matters Despite These Limitations

Recovering 14 of 16 bytes — using $3{,}000$ traces on hardware costing under $50 — from an algorithm NIST specifically vetted and standardized for its mathematical security is itself the core finding of this project. It demonstrates concretely, rather than abstractly, that **algorithmic security and implementation security are separate properties**, and that the gap between them is exploitable by relatively modest resources when a software implementation has no side-channel countermeasures.

## 11.14 Lessons Learned

- A correct Boolean leakage model matters more than a large variance peak — the two are not interchangeable.
- Pearson correlation, not variance, is the right tool for locating the actual leakage-bearing sample.
- Progressive recovery makes an otherwise intractable permutation-based cipher approachable one intermediate at a time.
- Trace alignment (via precise, tightly-scoped trigger placement) is a prerequisite, not an afterthought.
- Practical side-channel work is as much about careful engineering — firmware build systems, buffer sizing, trigger placement — as it is about the underlying statistics.

## 11.15 Chapter Summary

This chapter examined every layer of the attack — hardware, firmware, leakage model, trace count, and methodology — for the specific constraints that shaped the final 14-of-16 outcome. The two unrecovered bytes are most plausibly attributed to weaker leakage or insufficient trace count rather than a modeling error, and several concrete next steps (more traces, a refined window, alternative leakage models) follow directly from this analysis. [Chapter 12](12_Development_Journey.md) closes the repository with a full summary, the project's main contributions, and a roadmap for extending this work.

---

*Next: [Chapter 12 — Conclusion and Future Work](12_Development_Journey.md)*
